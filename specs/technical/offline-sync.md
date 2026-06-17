# Offline Sync Strategy — [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This document defines the bidirectional sync strategy between the [client platform] and the [backend platform], introduced in Phase 2. In Phase 1, the app operates entirely offline with [local DB] as the sole source of truth. This document has no impact on Phase 1 behavior — all sync fields (`last_synced_at`, `sync_status`) are present in the Phase 1 schema but remain null and inactive until Phase 2.

The sync strategy is designed to support an offline-first experience where the device always remains operational without network access, and data is synchronized with the server opportunistically when connectivity is available.

---

## Goals

- Allow the [client] to operate fully offline at all times, with sync occurring in the background when connectivity is available.
- Propagate local changes (creates, updates, soft deletes) to the backend reliably and in the correct entity dependency order.
- Pull server-side changes incrementally using a watermark approach, avoiding full re-downloads on every sync.
- Detect and surface conflicts to the user when the same record has diverged on both client and server, while avoiding false conflicts caused by semantically identical records.
- Ensure all sync operations are idempotent — retrying a failed sync produces no duplicate or corrupted data.
- Ensure exactly one sync cycle is active at any given time — concurrent sync operations are not permitted.
- Guarantee safe recovery from mid-sync interruptions (app close, process kill, worker cancellation) with no data loss or corruption.
- Avoid blocking the UI at any point during sync; sync runs asynchronously and status is communicated passively.

> Adjust goals to match your sync complexity. Remove goals that don't apply (e.g. skip conflict resolution goals if you use a simpler last-write-wins strategy with no user involvement).

---

## Non-Goals

- **Real-time sync**: No WebSocket or push-notification-based live sync in Phase 2. Sync is trigger-based (see Sync Triggers).
- **Attachment sync**: No file or media attachment sync in any current phase.
- **Selective sync**: All entities are synced in full; windowed or partial sync is a future consideration.
- **Automatic conflict resolution beyond server-wins**: Complex merge strategies are out of scope. CONFLICT state requires explicit user action.
- **Cross-[tenant] sync**: Each [tenant] is synced independently.
- **[Domain-specific exclusion, e.g. exchange rate sync]**: [explanation]
- **Device clock correction**: Phase 2 does not validate or correct device clock skew. This is a known limitation.

---

## Related Documents

| Document | Purpose |
|---|---|
| SPEC.md | Global business rules and feature index |
| ARCHITECTURE.md | Stack decisions, sync strategy overview, security model |
| specs/technical/data-model.md | Full schema including sync fields |
| specs/technical/api-contract.md | Pull and push endpoint definitions |
| specs/features/auth/requirements.md | JWT requirements and token lifecycle |
| specs/features/sync/requirements.md | Feature-level sync UI spec |

---

## Core Principles

### Offline First

The app must function without network access at all times. Every feature works entirely against the local database. Sync is always a background operation — never a prerequisite for reading or writing data.

### Local Database as Source of Truth

[Local DB] is the single source of truth on the device. All reads and writes go through the local DB first. The backend reflects the device's state after a successful sync, not the other way around.

### Eventual Consistency

The app guarantees that, given enough connectivity, all devices sharing a [tenant] will converge to the same state. It does not guarantee immediate consistency across devices.

### Server Authority

When a conflict is detected — the same record modified on both client and server since the last sync — the server version wins by default. The exception is records flagged as `CONFLICT`, which require explicit user resolution before the server version is applied (see Conflict Resolution).

### Soft Delete Propagation

Deletions are always soft — a `deleted_at` timestamp is set, never a hard delete. Soft-deleted records are included in sync payloads so that deletions propagate to all devices and the backend.

### Idempotency

All sync operations must be safe to retry. The backend uses the entity's UUID as the idempotency key — an upsert (create or update based on `id`) is performed on every received record.

### Single Sync at a Time

Exactly one sync cycle must be active at any given time per [tenant]. Concurrent sync operations are not permitted. This is enforced via a [concurrency primitive, e.g. Mutex] in the `SyncOrchestrator`.

### Interruption Safety

Sync must be safe to interrupt at any point. Partial progress is preserved at the record level. The watermark is only advanced on full successful completion, so interrupted pulls are always retried in full on the next trigger.

---

## Sync Lifecycle

### Local Write Flow

1. User creates, updates, or soft-deletes a record in the UI.
2. The [ViewModel / Controller] calls the relevant use case, which writes to [local DB] via the Repository.
3. [Local DB] persists the record immediately. `updated_at` is set to the current UTC timestamp on the device.
4. `sync_status` is set to `PENDING`. `last_synced_at` remains unchanged.
5. The UI reflects the change immediately — no waiting for network.

### Sync Trigger Flow

1. A sync trigger fires (see Sync Triggers).
2. The `SyncOrchestrator` attempts to acquire the sync lock (see Sync Concurrency Policy).
3. If another sync is already in flight: the trigger is handled per concurrency policy (dropped or queued).
4. If the lock is acquired: the `SyncOrchestrator` checks for network connectivity.
5. If offline: the lock is released; sync is deferred until connectivity is restored.
6. If online: the `SyncOrchestrator` begins the push flow, followed by the pull flow.

### Push Flow

1. Query local DB for all records where `sync_status IN (PENDING, FAILED)` or `sync_status IS NULL` (Phase 1 records with no sync_status set).
2. Group records by entity type. Sort within each entity type by `updated_at` ascending (oldest changes first).
3. Send records to the backend in batches (see Performance Considerations).
4. Parse the response per record:
   - **Accepted**: set `sync_status = SYNCED`, set `last_synced_at` to current UTC.
   - **Conflict**: set `sync_status = CONFLICT`. Queue for conflict resolution flow.
   - **Rejected** (validation failure): set `sync_status = FAILED`. Surface to user as non-retryable.
5. Proceed to pull flow.

### Pull Flow

1. Read the current pull watermark (`last_pull_at`) from local preferences.
2. Request all records updated on the server after the watermark, paginated.
3. For each received record:
   - If the local record has `sync_status = CONFLICT`: skip (do not overwrite pending user resolution).
   - If the local record is semantically identical to the server record (same values, different `updated_at`): update `sync_status = SYNCED` and `last_synced_at` only — do not trigger a push for this record.
   - Otherwise: apply the server record to local DB. Set `sync_status = SYNCED`, `last_synced_at` to current UTC.
4. Continue paginating until no more records are returned.
5. Advance the watermark to the server's reported `sync_time`.

### Cancellation Behavior

- **Graceful close**: the current record batch completes before the coroutine is cancelled. Progress is preserved.
- **Process kill**: the watermark is not advanced. The next sync retries the pull in full from the previous watermark. No data loss — the push may re-send already-accepted records, which the backend handles idempotently.
- **Worker cancellation** (e.g. [WorkManager / background job]): handled cooperatively via coroutine cancellation. Record-level progress from the current push batch is preserved.

---

## Sync Triggers

| Trigger | Condition | Behavior |
|---|---|---|
| Connectivity restored | Device goes online | Immediate sync attempt |
| App foreground | App returns from background | Sync if last sync > [N] minutes ago |
| Periodic background | Every [N] minutes/hours | [Background job] scheduled sync |
| Manual | User taps "Sync now" | Immediate sync attempt; UI shows status |
| Post-write | After any local write | [Optional: immediate or debounced push] |

> Define which triggers are active in Phase 2 and which are deferred.

---

## Conflict Resolution

A conflict occurs when the same record has been modified on both the client and the server since the last successful sync — i.e. the server's `updated_at` for a record is newer than the client's `last_synced_at` for that same record, and the client also has local changes.

### Detection

On push, the backend compares incoming `updated_at` against the stored `updated_at` for the same `id`. If the stored record is newer, a conflict is returned for that record.

### Default Resolution: Server Wins

By default, the server version is applied and the local version is discarded. This is the safe default for most records.

### User-Resolved Conflicts

Records flagged as `CONFLICT` are surfaced to the user with a side-by-side comparison of the local and server versions. The user chooses which version to keep. Selecting the local version triggers a push with `force: true`, bypassing conflict detection for that record.

### Semantic Equality Check

Before flagging a conflict, the client checks whether the local and server records are semantically identical (all user-visible fields match), even if `updated_at` differs. If so, the record is marked `SYNCED` without requiring user action — avoiding spurious conflicts caused by clock drift.

---

## Sync Metadata

Fields present on every entity from Phase 1 for sync purposes:

| Field | Type | Purpose |
|---|---|---|
| `last_synced_at` | INTEGER (UTC) | Timestamp of last successful sync for this record. Null until first sync. |
| `sync_status` | TEXT (ENUM) | Current sync state: PENDING, SYNCED, FAILED, CONFLICT. Null for Phase 1 records. |
| `updated_at` | INTEGER (UTC) | Last mutation time on the device. Used as conflict detection key. |
| `deleted_at` | INTEGER (UTC) | Soft delete timestamp. Included in sync payloads so deletions propagate. |

**Client-Only Fields:**
`sync_status` and `last_synced_at` are client-side tracking fields. They are never included in push payloads sent to the backend, and the backend never returns them in pull responses. They exist solely to track sync state on the device.

---

## Entity Dependency Order

Push and pull must respect entity dependency order to avoid FK violations. Entities are processed in this sequence:

1. [Root entity, e.g. Workspace / Tenant]
2. [Multi-user entity if applicable, e.g. WorkspaceMember] *(Phase 2)*
3. [Independent entities with no FK dependencies, e.g. Category, Tag]
4. [Entities depending on #3, e.g. Account]
5. [Entities depending on #4, e.g. Transaction]
6. [Junction/child entities, e.g. GoalContribution, LineItem]

> Pull applies records in this order (root → leaf). Push sends in the same order. Document your actual entities here at Phase 2 kickoff.

---

## Sync Concurrency Policy

| Scenario | Behavior |
|---|---|
| Trigger fires while sync in flight | New trigger is dropped (no-op). The in-flight sync covers it. |
| Manual sync while sync in flight | Show "Sync in progress" in UI. No duplicate sync started. |
| Sync completes while another trigger queued | Queued trigger fires immediately after completion. |

A [concurrency primitive, e.g. Mutex] in `SyncOrchestrator` enforces single-sync-at-a-time. Background job scheduling uses [ExistingPeriodicWorkPolicy.KEEP or equivalent] to prevent duplicate workers.

---

## Push Endpoint Expectations

- **Method**: `POST /api/v[N]/sync/push`
- **Auth**: Bearer JWT + `X-[Tenant]-ID` header
- **Body**: entity-keyed map of record arrays
- **Response shape**: entity-keyed map of `{ accepted: [ids], conflicts: [{id, server_record}], rejected: [{id, error}] }`
- `sync_status` and `last_synced_at` are never included in push payloads.

## Pull Endpoint Expectations

- **Method**: `GET /api/v[N]/sync/pull?since=[watermark]`
- **Auth**: Bearer JWT + `X-[Tenant]-ID` header
- **Response**: paginated, entity-keyed map of changed records since `since`
- Pull response never includes `sync_status` or `last_synced_at` — these are client-only fields.

---

## Android Client Responsibilities

> Adapt this section for your client platform.

### `SyncOrchestrator`

The central coordinator for all sync operations. Responsibilities:

- Querying `PENDING`, `FAILED`, and `NULL` sync_status records from the local DB.
- Performing the semantic equality check during pull application.
- Calling push and pull remote data sources.
- Applying pull results to the local DB via local data sources.
- Updating `sync_status` and `last_synced_at` per record.
- Advancing the watermark only after a complete, successful pull cycle.
- Exposing a `StateFlow<SyncState>` (or equivalent) for the UI to observe.

### Background Job / Worker

[Background job framework, e.g. WorkManager / BullMQ / Celery] manages periodic and connectivity-triggered background sync. Cancellation is handled cooperatively via [cancellation mechanism]. Partial record-level progress is preserved on cancellation.

### Connectivity Monitoring

A [connectivity observer] monitors network availability and notifies the `SyncOrchestrator` on connectivity restoration.

---

## Backend Responsibilities

### Conflict Detection

On receiving a push record, the backend compares the incoming `updated_at` against the stored `updated_at` for the same `id`. If the stored record is newer, a conflict is returned for that record. The `force: true` flag bypasses conflict detection for explicit user-resolved pushes.

### Validation

The backend validates all incoming records against the schema defined in `specs/technical/data-model.md`. Invalid records are rejected with `HTTP 400` and a per-record error. Validation failures are non-retryable and are surfaced to the user.

### Server Timestamps

The server stores the client-provided `updated_at` value as-is. It does not overwrite it with the server's own clock. In addition, the server records a `server_received_at` timestamp on every incoming record — a backend-only field, never synced to clients. Used for debugging, telemetry, and audit.

### [Tenant] Isolation

All pull and push operations are scoped to the authenticated user's [tenant] (`[tenant_id]` derived from the JWT and `X-[Tenant]-ID` header). The backend rejects any request that attempts to read or write records belonging to a [tenant] the user is not an `ACTIVE` member of.

---

## Performance Considerations

### Batch Sizes

Default push batch size: [N] records per request. Default pull page size: [N] records per response. Both are configurable via remote config in a future phase.

### Payload Size

[Describe expected payload characteristics — amount storage type, timestamp type, presence of blobs, expected per-record byte size.]

### Large Dataset Scaling

Phase 1 and 2 use full local mirroring of all [tenant] data. For long-term scaling, a windowed sync strategy (e.g. last [N] months on device, older data fetched on demand) is a future option. Revisit at Phase 2 kickoff based on data volume projections and target device specs.

---

## Security Considerations

### HTTPS Only

All sync traffic is transmitted over HTTPS. Plain HTTP is not accepted by the backend.

### Encrypted Token Storage

JWTs used to authenticate sync requests are stored in [encrypted storage mechanism, e.g. `EncryptedSharedPreferences`]. Tokens are never stored in plain text.

### [Tenant] Isolation

The backend enforces [tenant] isolation at the query layer — all sync operations are scoped to the `[tenant_id]` derived from the validated request headers, cross-referenced against the authenticated user's membership record.

### Sensitive Data

[Domain] data is never logged in plain text on the client or server. No [domain] data is transmitted to any third-party analytics or crash reporting service in any phase (NFR-DS-01 and its Phase 2 extension).

---

## Future Enhancements

- **Real-Time Sync**: WebSocket or server-sent events for live sync across devices. Not in scope for Phase 2.
- **Push Notifications**: Trigger a sync on receiving device when another device pushes a change. Depends on [notification service, e.g. FCM / APNs].
- **Selective Sync**: Allow the user to choose which [entities or date ranges] are synced to a given device.
- **Attachment Sync**: Sync [files/receipts/media] via [storage service]. Depends on [attachment feature].
- **Clock Skew Correction**: Detect and compensate for device clock drift to improve conflict detection reliability. Not in scope for Phase 2.

---

## Open Questions

- [Unresolved pull page size — hardcoded or configurable from the start?]
- [Per-entity sync status in UI vs. single workspace-level sync state?]
- [Permanently failed records: manual user action to reset, or auto-reset on app restart?]
- [Define maximum age threshold for purging soft-deleted records at Phase 2 kickoff.]
- [Conflict resolution UI: inline on record detail screen, or dedicated conflict queue screen?]

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
