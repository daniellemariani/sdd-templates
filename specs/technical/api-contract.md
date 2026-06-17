# API Contract — [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This document is the canonical API contract for the [App Name] backend ([framework, e.g. FastAPI]). It defines all endpoints, request/response schemas, validation rules, and sync behavior for Phase 2 and beyond.

This document owns the **transport layer** — the wire format exchanged between clients and the server. Transport schemas defined here are distinct from local DB entities and domain models used internally by clients, and from the canonical database schema in `specs/technical/data-model.md`. They may look similar initially but are independently versioned and may diverge as the API evolves.

All clients — [Platform 1] (Phase 2), [Platform 2] (Phase 3), [Platform 3] (Phase 4) — consume this API. Any change to this contract must be evaluated for impact across all current and future clients.

The sync strategy driving the push/pull endpoints is fully specified in `specs/technical/offline-sync.md`. This document translates those requirements into concrete endpoint definitions.

---

## Related Documents

| Document | Purpose |
|---|---|
| SPEC.md | Global business rules and feature index |
| ARCHITECTURE.md | Stack decisions, auth flow, API design conventions |
| specs/technical/data-model.md | Canonical database schema — source of truth for field definitions |
| specs/technical/offline-sync.md | Sync strategy, conflict resolution, watermark approach |
| specs/features/auth/requirements.md | Full auth feature spec (Phase 2) |
| specs/features/sync/requirements.md | Sync UI spec — conflict resolution UI, sync status indicators |

---

## Design Conventions

These conventions apply globally to every endpoint. Individual endpoint definitions do not repeat them.

### Base URL and Versioning

All endpoints are versioned under `/api/v1/`. See the **Versioning Strategy** section for compatibility guarantees.

```
https://<host>/api/v1/
```

### Naming

- Endpoint paths are lowercase hyphenated (e.g. `/goal-contributions`, not `/goalContributions`)
- Resource names are plural nouns (e.g. `/accounts`, `/transactions`)
- Sub-resources are nested only when the parent ID is required for context

### Required Headers

Every authenticated request must include the following headers:

| Header | Format | Description |
|---|---|---|
| `Authorization` | `Bearer <access_token>` | Auth-provider-issued JWT identifying the user |
| `X-[Tenant]-ID` | UUID v4 string | The active [tenant/workspace] for this request |

**`X-[Tenant]-ID` behavior:**
- The server validates that the authenticated user is an `ACTIVE` member of the specified [tenant] before processing any request.
- Required from Phase 2 to avoid a breaking change when multi-[tenant] is introduced in Phase 4.
- In Phase 4, the client switches [tenants] by changing this header value — no token exchange required.
- Requests with a missing or invalid `X-[Tenant]-ID` return `403 Forbidden`.
- Exception: [any endpoint exempt from tenant validation, e.g. invite acceptance]

### Request Format

- All request bodies are JSON (`Content-Type: application/json`)
- All requests must include required headers (see above)
- Query parameters are URL-encoded, used on `GET` requests only — `POST`, `PATCH`, and `DELETE` use JSON request bodies

### Response Format

- All responses are JSON
- HTTP status codes are used semantically (see Error Reference)
- All timestamps are Unix timestamps (integers, UTC)
- All monetary amounts are integers in smallest currency unit (e.g. cents)

### Response Envelope

All responses share a consistent envelope. Clients always read from `data`.

**Single resource:**
```json
{
  "data": { "...resource object..." },
  "meta": {
    "request_id": "string (UUID — for tracing and support)",
    "timestamp": "integer (Unix UTC — when the server processed the request)"
  }
}
```

**List resource:**
```json
{
  "data": ["array of resource objects"],
  "pagination": { "...PaginationSchema..." },
  "meta": {
    "request_id": "string (UUID)",
    "timestamp": "integer (Unix UTC)"
  }
}
```

`meta` is present on every response and is never null.

### PATCH Semantics

All `PATCH` endpoints follow these rules globally:

- **Field omitted** → property is unchanged
- **Field present with a value** → property is updated to that value
- **Field present as `null`** → property is cleared (only valid on nullable fields; sending `null` on a non-nullable field returns `400 VALIDATION_ERROR`)

Individual endpoint definitions do not repeat these rules. Only immutability constraints are called out per endpoint.

### [Tenant] Scoping

Every endpoint that returns or mutates [domain] data is scoped to the [tenant] identified by `X-[Tenant]-ID`. The server derives `[tenant_id]` from this header — validated against the authenticated user's membership record — not from JWT claims directly.

### Soft Deletes

`DELETE` on any resource endpoint triggers a **soft delete** only — `deleted_at` is set to the current UTC timestamp. No record is ever hard-deleted via the API. Soft-deleted records are excluded from standard list responses but are included in sync pull payloads.

### Pagination

Standard list endpoints use offset-based pagination. The sync pull endpoint uses cursor-based pagination.

**Offset pagination schema:**
```json
{
  "page": "integer (1-based)",
  "page_size": "integer",
  "total": "integer",
  "has_next": "boolean"
}
```

**Cursor pagination schema (sync pull):**
```json
{
  "next_cursor": "string | null",
  "has_more": "boolean"
}
```

---

## Versioning Strategy

### Current Version

The API is currently at `v1`. All endpoints live under `/api/v1/`.

### Backward Compatibility

Within `v1`, all changes must be **additive and non-breaking**:

- New optional fields may be added to request or response schemas
- New endpoints may be added
- Existing required fields must not be removed or renamed
- Existing response field types must not change
- Enum values must not be removed; new values may be added and clients must handle unknown enum values gracefully

### Breaking Changes

Breaking changes require a new API version (`v2`). A breaking change is any change that would cause an existing client to malfunction if not updated, including: removing or renaming a required field, changing a field type, changing HTTP method or status codes, removing an endpoint.

---

## Authentication & Authorization

### Identity Layer

User identity is managed by [auth provider, e.g. Supabase Auth]. The backend validates every incoming JWT locally using `[JWT_SECRET]`. No network call to the auth provider is made per request. Validation checks: signature, expiry (`exp`), and issuer (`iss`). Expired or malformed tokens return `401 Unauthorized`.

### Role Permission Matrix

| Action | OWNER | ADMIN | MEMBER | VIEWER |
|---|---|---|---|---|
| Read [core entities] | ✓ | ✓ | ✓ | ✓ |
| Create / update / delete [core entities] | ✓ | ✓ | ✓ | ✗ |
| Manage [config entities] | ✓ | ✓ | ✗ | ✗ |
| Manage members | ✓ | ✓ | ✗ | ✗ |
| Transfer ownership | ✓ | ✗ | ✗ | ✗ |

> Copy the role permission matrix from SPEC.md. Adapt columns to your role model. Each endpoint definition references the minimum required role.

---

## Shared Schemas

### Error Schema

```json
{
  "error": {
    "code": "string (e.g. VALIDATION_ERROR, NOT_FOUND, FORBIDDEN)",
    "message": "string (human-readable)",
    "details": "object | null (field-level validation errors)"
  },
  "meta": {
    "request_id": "string",
    "timestamp": "integer"
  }
}
```

### Error Reference

| HTTP Status | Code | When used |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Request body fails validation |
| 401 | `UNAUTHORIZED` | Missing, expired, or invalid token |
| 403 | `FORBIDDEN` | Valid token but insufficient role or wrong tenant |
| 404 | `NOT_FOUND` | Resource does not exist or is soft-deleted |
| 409 | `CONFLICT` | Unique constraint violation |
| 429 | `RATE_LIMITED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Unexpected server error |

---

## Sync API

> The full sync strategy is defined in `specs/technical/offline-sync.md`. This section translates it into endpoint definitions.

### Canonical Sync Order

Entities are pushed and pulled in dependency order to avoid FK violations:

1. [Root entity, e.g. Workspace]
2. [Multi-user entity, e.g. WorkspaceMember] *(Phase 2)*
3. [Independent entities — no FKs, e.g. Category, Tag]
4. [Entities depending on #3]
5. [Leaf entities — most FKs]

### Push Record Schema

> Fields that are client-only tracking state (`sync_status`, `last_synced_at`) are NEVER included in push payloads.

```json
{
  "id": "string (UUID)",
  "updated_at": "integer (Unix UTC)",
  "deleted_at": "integer | null",
  "[field]": "...",
  "force": "boolean (optional, default false — bypasses conflict detection)"
}
```

### Pull Record Schema

> Fields that are client-only tracking state (`sync_status`, `last_synced_at`) are NEVER included in pull responses.

```json
{
  "id": "string (UUID)",
  "updated_at": "integer (Unix UTC)",
  "deleted_at": "integer | null",
  "[field]": "..."
}
```

### POST /api/v1/sync/push

**Purpose:** Push local changes to the backend.

**Auth requirements:** MEMBER or above

**Request schema:**
```json
{
  "[entity_type_plural]": [
    { "...Push Record Schema..." }
  ]
}
```

**Response schema:**
```json
{
  "[entity_type_plural]": {
    "accepted": ["string (ids)"],
    "conflicts": [
      {
        "id": "string",
        "server_record": { "...Pull Record Schema..." }
      }
    ],
    "rejected": [
      {
        "id": "string",
        "error": "string"
      }
    ]
  },
  "meta": { "...Metadata..." }
}
```

### GET /api/v1/sync/pull

**Purpose:** Pull server changes since last sync watermark.

**Auth requirements:** VIEWER or above

**Query parameters:**
- `since` (integer, Unix UTC) — watermark. Returns all records updated after this timestamp.
- `cursor` (string, optional) — for paginated pull responses
- `page_size` (integer, optional, default [N]) — records per page

**Response schema:**
```json
{
  "[entity_type_plural]": [
    { "...Pull Record Schema..." }
  ],
  "sync_time": "integer (Unix UTC — use this as the new watermark after a complete pull cycle)",
  "pagination": { "...cursor pagination schema..." },
  "meta": { "...Metadata..." }
}
```

---

## [Feature] API

> Use this section pattern for every feature's CRUD endpoints.
> Group by resource. List endpoints in order: GET list, GET single, POST, PATCH, DELETE.

### [Entity] Canonical Object

The canonical [Entity] object returned by all endpoints:

```json
{
  "id": "string (UUID)",
  "[tenant_id]": "string (UUID)",
  "[field_1]": "[type]",
  "[field_2]": "[type]",
  "created_at": "integer (Unix UTC)",
  "updated_at": "integer (Unix UTC)",
  "deleted_at": "integer | null"
}
```

---

#### GET /api/v1/[entities]

**Purpose:** List all [entities] in the [tenant].

**Auth requirements:** VIEWER or above

**Query parameters:**
- `page` (integer, default 1)
- `page_size` (integer, default 20, max 100)
- `[filter_field]` (type, optional) — [description]

**Sort order:** [e.g. Pinned items first (pinned_at ascending), then by name ascending]

**Response schema:**
```json
{
  "data": ["array of canonical [Entity] objects"],
  "pagination": { "...PaginationSchema..." },
  "meta": { "...Metadata..." }
}
```

---

#### GET /api/v1/[entities]/{[entity]_id}

**Purpose:** Retrieve a single [entity].

**Auth requirements:** VIEWER or above

**Response schema:**
```json
{
  "data": { "...canonical [Entity] object..." },
  "meta": { "...Metadata..." }
}
```

HTTP 404 if the [entity] does not exist or is soft-deleted.

---

#### POST /api/v1/[entities]

**Purpose:** Create a new [entity].

**Auth requirements:** [MEMBER or above / role that can create this entity]

**Request schema:**
```json
{
  "[required_field]": "[type]",
  "[optional_field]": "[type] (optional)"
}
```

**Response schema:**
```json
{
  "data": { "...created [Entity] object..." },
  "meta": { "...Metadata..." }
}
```

HTTP 201.

**Validation rules:**
- `[field]` must be [constraint]
- `[field]` must be non-empty

**Business rule references:** BR-[XX]-NN

---

#### PATCH /api/v1/[entities]/{[entity]_id}

**Purpose:** Update a [entity].

**Auth requirements:** [MEMBER or above]

**Request schema:**
```json
{
  "[updatable_field_1]": "[type]",
  "[updatable_field_2]": "[type] | null"
}
```

**Immutable fields:** `[field]`, `[field]` — cannot be changed after creation

**Server-managed fields:** `[field]` — never accepted in request body; set by the server on [condition]

**Response schema:**
```json
{
  "data": { "...updated [Entity] object..." },
  "meta": { "...Metadata..." }
}
```

**Validation rules:**
- `[field]` must be [constraint] if provided

**Business rule references:** BR-[XX]-NN

---

#### DELETE /api/v1/[entities]/{[entity]_id}

**Purpose:** Soft-delete a [entity].

**Auth requirements:** [MEMBER or above]

**Response schema:** HTTP 204 No Content

**Business rule references:** BR-[XX]-NN, BR-DI-01

---

### [Phase 2 Feature] API *(Phase 2 stub)*

[Description of the feature and when it is fully specified.]

Full spec: `specs/features/[feature]/requirements.md`.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/v1/[resources]` | List all [resources] |
| `GET` | `/api/v1/[resources]/{id}` | Retrieve a single [resource] |
| `POST` | `/api/v1/[resources]` | Create a [resource] |
| `PATCH` | `/api/v1/[resources]/{id}` | Update a [resource] |
| `DELETE` | `/api/v1/[resources]/{id}` | Soft-delete a [resource] |

---

## Security Considerations

### HTTPS Enforcement

All API traffic must occur over HTTPS. The backend rejects plain HTTP connections.

### JWT Validation

The backend validates every incoming JWT locally using `[JWT_SECRET]`. No network call to the auth provider is made per request. Validation checks: signature, expiry (`exp`), and issuer (`iss`). Expired or malformed tokens return `401 Unauthorized`.

### [Tenant] Isolation

All queries are filtered by `[tenant_id]` derived from the validated `X-[Tenant]-ID` header, cross-referenced against the authenticated user's membership record. This is enforced at the service and query layer — not just at the route level.

### [Domain] Data Protection

- [Domain] data is never included in server logs in plain text
- No [domain] data is transmitted to third-party analytics, crash reporting, or monitoring services
- This policy applies in all phases (NFR-DS-01 and its Phase 2 extension)

### Rate Limiting

The API enforces rate limits per authenticated user. When the limit is exceeded, the server returns `429 Too Many Requests` with a `Retry-After` header.

### Input Validation

All incoming request bodies are validated against the schemas defined in this document before any database operation is performed. Validation failures return `400 VALIDATION_ERROR` with field-level detail in `error.details`. The server never trusts client-supplied `[tenant_id]` in request bodies — it is always derived from the validated header.

---

## Open Questions

- [Timezone handling for period/date-based queries — UTC or user local time?]
- [Text search support — `q` parameter on list endpoints? Deferred to Phase 3.]
- [Cascade behavior on soft-delete — does deleting parent cascade to children?]
- [Transactional email provider — decision at Phase 2 kickoff]
- [Rate limit thresholds — define at Phase 2 kickoff]
- [Breaking change migration strategy — how are v1 → v2 transitions communicated to clients?]

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
