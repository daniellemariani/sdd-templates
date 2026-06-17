# ROADMAP.md — [App Name]

**Version:** 0.0.1
**Status:** [Not Started / In Progress / On Hold]
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

## Phase 1 — [Platform, e.g. Android Offline] (Current)
- [ ] Project setup ([key tooling, e.g. monorepo, Gradle, Hilt, Room, base package structure])
- [ ] [feature-1]/ — see specs/features/[feature-1]/tasks.md
- [ ] [feature-2]/ — see specs/features/[feature-2]/tasks.md
- [ ] [feature-3]/ — see specs/features/[feature-3]/tasks.md
- [ ] [feature-4]/ — see specs/features/[feature-4]/tasks.md

## Phase 2 — [Platform, e.g. Backend + Sync]
- [ ] Project setup ([key tooling, e.g. FastAPI, Supabase, Alembic, CI])
- [ ] [phase-2-only feature, e.g. auth]/ — see specs/features/[feature]/tasks.md
- [ ] [phase-2-only feature, e.g. sync]/ — see specs/features/[feature]/tasks.md
- [ ] [feature-1]/ — see specs/features/[feature-1]/tasks.md
- [ ] [feature-2]/ — see specs/features/[feature-2]/tasks.md
- [ ] [feature-3]/ — see specs/features/[feature-3]/tasks.md
- [ ] [feature-4]/ — see specs/features/[feature-4]/tasks.md

> Note: Phase 2 typically re-lists every Phase 1 feature, since each one needs a backend counterpart (API endpoints, sync support) in addition to any new Phase 2-only features.

## Phase 3 — [Platform, e.g. Web]
- [ ] Project setup ([key tooling, e.g. React, Vite, TanStack Query, shadcn/ui, hosting])
- [ ] [feature-1]/ — see specs/features/[feature-1]/tasks.md
- [ ] [feature-2]/ — see specs/features/[feature-2]/tasks.md
- [ ] [feature-3]/ — see specs/features/[feature-3]/tasks.md
- [ ] [feature-4]/ — see specs/features/[feature-4]/tasks.md
- [ ] [phase-3-only feature, e.g. web-dashboard]/ — see specs/features/[feature]/tasks.md
- [ ] [phase-3-only feature, e.g. reports]/ — see specs/features/[feature]/tasks.md

## Phase 4 — [Platform, e.g. Cross-Platform Mobile]
- [ ] [Major open decision, e.g. Flutter or KMP decision]
- [ ] Project setup (TBD based on [decision])
- [ ] Migration of Phase 1 [Platform 1] features to cross-platform
- [ ] [Phase 4-only capability, e.g. Multi-tenant/workspace UI]

---

> **Usage notes:**
> - This file tracks feature-level progress only — implementation detail lives in each feature's `tasks.md`.
> - Checkboxes are checked when a feature's entire task list is complete, not partially.
> - Feature order within a phase should follow the dependency order recommended in SPEC.md (e.g. independent entities before the entities that reference them).
> - Update `Last Updated` and bump `Version` whenever a phase's feature list changes — not on every checkbox tick.
