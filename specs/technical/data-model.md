# Data Model — [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This document is the canonical database schema reference for [App Name]. It defines every entity, field, constraint, and relationship across all phases. All other documents (feature specs, API contract) reference this file — they do not duplicate schema definitions.

Phase 1 entities are fully defined. Phase 2+ entities are stubbed with enough detail to inform Phase 1 field decisions (e.g. tenant FK, sync fields).

---

## Related Documents

| Document | Purpose |
|---|---|
| SPEC.md | Global business rules and feature index |
| ARCHITECTURE.md | Stack decisions and data layer constraints |
| specs/technical/api-contract.md | API endpoint definitions |
| specs/technical/offline-sync.md | Sync strategy and conflict resolution |

---

## Design Principles

These principles are enforced at the database layer across all platforms and phases.

| Principle | Rule |
|---|---|
| Soft delete | All entities use `deleted_at` (nullable timestamp). Hard deletes are never performed. (BR-DI-01) |
| Audit timestamps | All entities include `created_at` and `updated_at`. (BR-DI-02) |
| Amounts as integers | All monetary values stored in smallest currency unit as `INTEGER`. Never `REAL` or `FLOAT`. (BR-DI-03) |
| UTC dates | All timestamps stored in UTC. Converted to device local time in the UI layer. (BR-DI-04) |
| Tenant isolation | All direct [domain] entities carry a `[tenant_id]` FK from Phase 1 for query performance and security boundary enforcement. (BR-[XX]-01) |
| Primary keys | All entities use UUID v4 (TEXT) as primary key, generated client-side at creation time. Ensures global uniqueness across devices without server coordination. Supports offline-first sync introduced in Phase 2. |
| Sync | All entities include `last_synced_at` (nullable INTEGER timestamp) and `sync_status` (nullable ENUM: PENDING, SYNCED, FAILED, CONFLICT), present from Phase 1 to avoid future migrations. Unused until Phase 2. |
| Derived values | The following fields are calculated at query time and never persisted: [list derived values, e.g. balances, progress %, totals, sort order]. |

> Add or remove principles to match your domain. Every principle should have a BR reference in SPEC.md.

---

## Entity Reference

> For each entity: provide a one-line description, state the introducing Phase, list all fields in a table, then list constraints. Do not duplicate business rule text — reference by BR ID only. Phase 2+ entities should be stubbed with a note.

---

### [Tenant Entity, e.g. Workspace]

[One-line description. E.g. "Top-level container for all [domain] data. A default [Tenant] is created on first launch and is not visible in the UI in Phase 1."]

**Phase:** 1

| Field | Type | Nullable | Default | Description |
|---|---|---|---|---|
| id | TEXT | No | UUID v4 | Primary key. Generated client-side at creation time. |
| name | TEXT | No | — | Display name |
| [domain_setting] | TEXT | No | [default] | [e.g. base currency, locale, theme preference] |
| created_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| updated_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| deleted_at | INTEGER | Yes | null | Soft delete timestamp, UTC |
| last_synced_at | INTEGER | Yes | null | Unix timestamp, UTC. Null in Phase 1. (Phase 2) |
| sync_status | TEXT | Yes | null | ENUM: PENDING, SYNCED, FAILED, CONFLICT. Null in Phase 1. (Phase 2) |

**Constraints:**
- `name` must be non-empty
- [Deletion constraint — e.g. "The default [Tenant] cannot be deleted (BR-[XX]-04)"]
- [Other structural constraint]

---

### [Core Entity 1, e.g. Account]

[One-line description.]

**Phase:** 1

| Field | Type | Nullable | Default | Description |
|---|---|---|---|---|
| id | TEXT | No | UUID v4 | Primary key. Generated client-side at creation time. |
| [tenant_id] | TEXT | No | — | FK → [Tenant].id |
| name | TEXT | No | — | User-defined display name |
| type | TEXT | No | — | ENUM: `[TYPE_A]`, `[TYPE_B]`, `[TYPE_C]` |
| [amount_field] | INTEGER | No | 0 | Amount in [smallest unit]. Always positive. |
| is_pinned | INTEGER | No | 0 | Boolean flag. 1 = pinned to top of list. (BR-PI-01) |
| pinned_at | INTEGER | Yes | null | Unix timestamp, UTC. Set when pinned; cleared when unpinned. (BR-PI-02) |
| created_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| updated_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| deleted_at | INTEGER | Yes | null | Soft delete timestamp, UTC |
| last_synced_at | INTEGER | Yes | null | Unix timestamp, UTC. Null in Phase 1. (Phase 2) |
| sync_status | TEXT | Yes | null | ENUM: PENDING, SYNCED, FAILED, CONFLICT. Null in Phase 1. (Phase 2) |

**Constraints:**
- `type` must be one of the defined ENUM values
- [Soft delete constraint if applicable — e.g. "Cannot be soft-deleted if it has associated [child entities]. (BR-XX-NN)"]
- `Unique on ([tenant_id], name) where deleted_at IS NULL`
- When `is_pinned = 1`, `pinned_at` must be non-null. When `is_pinned = 0`, `pinned_at` must be null. (BR-PI-03)

---

### [Core Entity 2]

[One-line description.]

**Phase:** 1

| Field | Type | Nullable | Default | Description |
|---|---|---|---|---|
| id | TEXT | No | UUID v4 | Primary key. Generated client-side at creation time. |
| [tenant_id] | TEXT | No | — | FK → [Tenant].id |
| [parent_id] | TEXT | No | — | FK → [Parent Entity].id |
| [amount_field] | INTEGER | No | — | Amount in [smallest unit]. Always positive. |
| notes | TEXT | Yes | null | Optional free-text note |
| created_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| updated_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| deleted_at | INTEGER | Yes | null | Soft delete timestamp, UTC |
| last_synced_at | INTEGER | Yes | null | Unix timestamp, UTC. Null in Phase 1. (Phase 2) |
| sync_status | TEXT | Yes | null | ENUM: PENDING, SYNCED, FAILED, CONFLICT. Null in Phase 1. (Phase 2) |

**Constraints:**
- [Constraint 1]
- [Constraint 2]

---

### [Phase 2 Entity] *(Phase 2)*

> Stub only — enough detail to inform Phase 1 field decisions. Full spec defined at Phase 2 kickoff.

| Field | Type | Nullable | Default | Description |
|---|---|---|---|---|
| id | TEXT | No | UUID v4 | Primary key. |
| [tenant_id] | TEXT | No | — | FK → [Tenant].id |
| [key_field] | TEXT | No | — | [Brief description] |
| created_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| updated_at | INTEGER | No | now (UTC) | Unix timestamp, UTC |
| deleted_at | INTEGER | Yes | null | Soft delete timestamp, UTC |
| last_synced_at | INTEGER | Yes | null | Unix timestamp, UTC. (Phase 2) |
| sync_status | TEXT | Yes | PENDING | ENUM: PENDING, SYNCED, FAILED, CONFLICT. |

---

## Entity Relationship Summary

```
[Tenant]
├── [Entity A] ([tenant_id])
├── [Entity B] ([tenant_id])
│   └── → [Entity A] ([fk_field])
├── [Entity C] ([tenant_id])
│   ├── → [Entity A] ([fk_field])
│   └── → [Entity B] ([fk_field])
└── [Entity D] ([tenant_id] — Phase 2)
    └── → [User] ([user_id] — Phase 2)
```

---

## Indexes

Indexes are listed per entity for fields that appear frequently in queries, filters, or joins.

| Entity | Index Fields | Rationale |
|---|---|---|
| [Entity] | `([tenant_id], [date_field])` | Date-range queries for period reporting |
| [Entity] | `([tenant_id], [fk_field])` | [Parent] history lookup |
| [Entity] | `([tenant_id], deleted_at)` | Soft delete filter on all list queries |
| [Entity] | `([tenant_id], is_pinned, pinned_at)` | Pinned sort order on list screen |

> Add one row per index. Rationale should explain the query pattern being optimized, not just repeat the field names.

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial entity draft |

---

> **Conventions reminder:**
> - IDs are never renumbered once assigned.
> - Derived values are never persisted — compute at query time.
> - Phase 2+ entities are stubs until Phase 2 kickoff.
> - All monetary values are integers (cents or smallest unit). Never float.
> - All timestamps are Unix integers in UTC.
