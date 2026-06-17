# ARCHITECTURE.md — [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

[One paragraph describing the primary architectural constraint and philosophy. State the single source of truth, the offline/online stance, and the phased approach rationale. E.g. "The primary architectural constraint is offline-first: [DB] is the single source of truth in Phase 1 and all features must function without network access. Backend and web layers are introduced later to avoid over-engineering before the core product is validated."]

---

## Repository Structure

[Monorepo or polyrepo? Document the top-level layout. Example below is monorepo.]

```
[project-root]/
├── [platform-1]/     # e.g. android/ — Phase 1
├── [platform-2]/     # e.g. backend/ — Phase 2
├── [platform-3]/     # e.g. web/ — Phase 3
├── specs/
│   ├── features/     # One subfolder per feature
│   └── technical/    # data-model.md, api-contract.md, offline-sync.md
├── .cursor/
│   └── rules/        # AI coding rules (.mdc files)
├── PRODUCT.md
├── SPEC.md
├── ARCHITECTURE.md
└── CONTEXT.md
```

---

## Phase Overview

| Phase | Platform | Backend | Status |
|---|---|---|---|
| 1 | [e.g. Android offline] | None | Not Started |
| 2 | [e.g. Android + sync] | [e.g. FastAPI + PostgreSQL] | Not Started |
| 3 | [e.g. Web dashboard] | [e.g. FastAPI + PostgreSQL] | Not Started |
| 4 | [e.g. Cross-platform mobile] | [e.g. FastAPI + PostgreSQL] | Not Started |

---

## [Platform 1] Architecture

> Example: Android Architecture

### Stack

| Layer | Technology |
|---|---|
| Language | [e.g. Kotlin] |
| UI | [e.g. Jetpack Compose] |
| Architecture Pattern | [e.g. MVVM + Repository] |
| Dependency Injection | [e.g. Hilt] |
| Local Database | [e.g. Room] |
| Async | [e.g. Coroutines + Flow] |
| Build | [e.g. Gradle (Kotlin DSL)] |
| Network | [e.g. Retrofit + OkHttp — Phase 2] |

### Architecture

[Describe the layered architecture with a diagram. Example:]

```
UI (Composables / Views)
    └── ViewModel              # UI state, user events, business logic coordination
        └── Use Case           # Single business operation
            └── Repository     # Data access interface
                └── DAO / DataSource
                    └── Local Database
```

- [Key architectural constraint or rule — e.g. "UI layer never accesses Repository directly"]
- [Key constraint — e.g. "All data access is asynchronous"]
- [Key constraint — e.g. "Use cases represent single business operations"]

### Domain Model Principles

- `core/domain/` contains pure [language] domain entities — no framework dependencies
- [Entity] lives in `core/data/` — maps to database columns
- Network DTOs live in `feature/data/remote/` — mirror API response structure (Phase 2)
- Mappers convert between domain models, DB entities, and DTOs at layer boundaries
- UI state models live in `feature/ui/`

### Package Structure

```
[root-package]/
├── core/
│   ├── ui/                    # Shared composables, theme, design tokens
│   ├── domain/                # Pure domain entities
│   └── data/
│       ├── AppDatabase.kt     # Database definition
│       ├── [Entity]Dao.kt
│       └── ...
├── feature/
│   ├── [feature-1]/
│   ├── [feature-2]/
│   └── ...
└── app/
    ├── MainActivity.kt
    ├── AppNavGraph.kt
    └── ...
```

Each feature folder follows the structure:

```
feature/[feature-name]/
├── ui/
│   ├── [Feature]Screen.kt
│   └── [Feature]ViewModel.kt
├── domain/
│   ├── [Feature]Repository.kt      # interface
│   ├── Create[Feature]UseCase.kt
│   ├── Update[Feature]UseCase.kt
│   ├── Delete[Feature]UseCase.kt
│   └── Get[Feature]UseCase.kt
└── data/
    ├── local/
    │   └── [Feature]LocalDataSource.kt
    └── [Feature]RepositoryImpl.kt
```

> Note: Feature `data/` includes `local/` in Phase 1. A `remote/` directory is added in Phase 2.

### Local Database

[Describe the local database setup and key constraints.]

- Database name: `[app-name].db`
- All entities include `created_at`, `updated_at`, and `deleted_at` (soft delete)
- Amounts stored as `INTEGER` (smallest currency unit, e.g. cents), never `REAL`
- Dates stored as `INTEGER` (Unix timestamp, UTC)
- `[tenant_id]` foreign key present on all entities from Phase 1
- Full schema: `specs/technical/data-model.md`

### Navigation

[Describe the navigation architecture. Example for Jetpack Compose:]

[Navigation library] with a nested nav graph architecture. `AppNavGraph` hosts the single `NavHost` and composes feature-owned nested nav graphs. Each feature defines its own nav graph extension and registers its internal routes independently. Features never import each other.

Top-level tab routes are defined as a sealed class in `TopLevelDestination`. Feature-internal routes are not exposed outside the feature's nav graph extension.

### Dependency Injection

[DI framework] provides the DI graph. Modules are defined per layer:

- `DatabaseModule` — provides DB and all DAOs
- `RepositoryModule` — binds Repository interfaces to implementations
- Feature-level modules where needed

### Offline-First Guarantee

Phase 1 has no network dependency of any kind. No feature may depend on network availability (NFR-OF-01, NFR-OF-02).

---

## [Platform 2] Architecture

> Example: Backend Architecture (FastAPI / Node / Rails / etc.)

### Stack

| Layer | Technology |
|---|---|
| Language | [e.g. Python] |
| Framework | [e.g. FastAPI] |
| ORM | [e.g. SQLModel] |
| Database | [e.g. PostgreSQL via Supabase] |
| Auth | [e.g. Supabase Auth / JWT] |
| Migrations | [e.g. Alembic] |
| Testing | [e.g. pytest] |

### Architecture

[Describe the backend pattern — layered, service-based, etc.]

- [Key constraint — e.g. "Business logic lives in service layer, not in route handlers"]
- [Key constraint — e.g. "Request and response schemas are defined as separate Pydantic models from DB models"]
- [Key constraint — e.g. "All authentication enforced via middleware"]

### Package Structure

```
[backend-root]/
├── app/
│   ├── api/
│   │   └── v1/
│   │       ├── routes/          # One file per feature
│   │       └── router.py
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py
│   │   └── database.py
│   ├── models/                  # ORM models
│   ├── schemas/                 # Request/response schemas
│   ├── services/                # Business logic
│   └── main.py
├── [migrations]/
├── tests/
└── [build-config]
```

---

## [Platform 3] Architecture

> Example: Web Architecture (React / Vue / etc.)

### Stack

| Layer | Technology |
|---|---|
| Language | [e.g. TypeScript] |
| Framework | [e.g. React] |
| Server State | [e.g. TanStack Query] |
| UI Components | [e.g. shadcn/ui] |
| Styling | [e.g. Tailwind CSS] |
| Build | [e.g. Vite] |
| Auth | [e.g. Supabase JS SDK] |
| Hosting | TBD |

### Architecture

- Feature-based folder structure
- [State management strategy — e.g. "No global client-side state manager unless a specific need arises"]
- [Auth flow — e.g. "JWT passed in Authorization header on every request"]

### Package Structure

```
[web-root]/src/
├── features/
│   └── [feature-name]/
│       ├── components/
│       ├── hooks/
│       ├── types.ts
│       └── index.ts
├── components/
│   └── ui/                      # Shared UI components
├── hooks/                       # Shared hooks
├── lib/
│   ├── api.ts                   # API client wrapper
│   └── utils.ts
├── types/                       # Shared TypeScript types
├── pages/                       # Route-level components
└── main.tsx
```

---

## [Platform 4] Architecture — Deferred

> Example: Cross-Platform Mobile (Flutter / KMP)

The decision between [Option A] and [Option B] is deferred to Phase [N]. Key considerations at decision time:

- [Option A]: [trade-offs]
- [Option B]: [trade-offs]

Decision Criteria:
- [Criterion 1]
- [Criterion 2]

---

## Cross-Cutting Concerns

### Data Integrity

Enforced at the database layer across all platforms:

- **Soft delete** — all entities use a `deleted_at` timestamp. Hard deletes are never performed. (BR-DI-01)
- **Audit timestamps** — `created_at` and `updated_at` on every entity. (BR-DI-02)
- **Amounts as integers** — all monetary values stored in smallest currency unit to avoid floating-point precision errors. (BR-DI-03)
- **UTC dates** — all timestamps stored in UTC, converted to local time in the UI layer. (BR-DI-04)

### [Tenant / Workspace] Isolation

- `[tenant_id]` is a foreign key on every [data] entity from Phase 1 forward.
- A default [Tenant] is created on first launch and is not exposed in the UI in Phase 1.
- This design avoids a migration when multi-[tenant] support is introduced in Phase [N].

### Error Handling

- [Platform 1]: errors surfaced to the UI via [e.g. sealed `UiState` classes]. No silent failures.
- Backend: consistent error response schema across all endpoints. HTTP status codes used semantically.
- [Platform 3]: [e.g. TanStack Query error states handled per feature; no global error boundary swallowing errors silently.]

### Security

- Phase 1: no data leaves the device (NFR-DS-01). No analytics, no crash reporting, no remote logging.
- Phase 2+: HTTPS only. Tokens stored in [e.g. `EncryptedSharedPreferences`]. No secrets in source code.
- [Domain] data never transmitted to third parties in any phase.

### Testing Strategy

| Platform | Unit | Integration | UI |
|---|---|---|---|
| [Platform 1] | [e.g. JUnit] | [e.g. In-memory DB tests] | Deferred |
| Backend | [e.g. pytest] | [e.g. TestClient + test DB] | N/A |
| [Platform 3] | [e.g. Vitest] | [e.g. Mock handlers] | Deferred |

> Test coverage targets and CI configuration defined at Phase 2 kickoff.

---

## Open Technical Decisions

- [Unresolved hosting or infrastructure choice]
- [Unresolved tooling or service decision]
- [Scaling or performance trade-off to revisit at Phase N kickoff]

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |

---

## Related Documents

| Document | Purpose |
|---|---|
| PRODUCT.md | Vision, users, success criteria |
| SPEC.md | Feature index, glossary, global business rules |
| CONTEXT.md | Session continuity and current state |
| specs/technical/data-model.md | Full database schema |
| specs/technical/api-contract.md | API endpoint definitions |
| specs/technical/offline-sync.md | Sync strategy and conflict resolution |
