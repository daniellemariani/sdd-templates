# Spec-Driven Development Templates

A set of reusable Markdown templates for running Spec-Driven Development (SDD) on a software project. They were extracted from a real, actively-maintained project and generalized for reuse — every template keeps the structure, conventions, and hard-won lessons of the original, with project-specific content replaced by `[bracketed placeholders]` and guidance comments.

The core idea behind SDD: write down architectural and product decisions before implementation begins, keep that documentation current as the single source of truth, and let an AI coding agent (or a human) implement against the spec rather than against vibes.

## Why use this

Specs decay the moment they stop being the easiest way to answer a question. These templates are built to keep documents from drifting apart as a project grows:

- **Permanent IDs.** Requirements, acceptance criteria, business rules, and tasks are numbered once and never renumbered. Old IDs can be deprecated but never reused.
- **Single source of truth per concept.** The app name lives in `SPEC.md`. The schema lives in `data-model.md`. Feature specs reference these by ID instead of repeating the text — so a change only has to be made in one place.
- **Three files per feature, no exceptions.** Every feature gets a `requirements.md`, `design.md`, and `tasks.md`. Splitting "what" from "how" from "do" keeps each file reviewable on its own.
- **Changelogs everywhere.** Every document ends with a changelog table. Future-you (or your agent) can see what changed and why without diffing the whole file.

## What's in this repo

| File | Purpose |
|---|---|
| `PRODUCT.md` | Problem statement, target users, core workflow, goals, non-goals, success criteria |
| `SPEC.md` | App name (canonical), glossary, user roles, feature index, global business rules, NFRs |
| `ARCHITECTURE.md` | Stack decisions per platform/phase, package structure, cross-cutting concerns |
| `ROADMAP.md` | Phase-by-phase feature checklist, linking out to each feature's `tasks.md` |
| `design.md` | Visual design system — philosophy, color, typography, spacing, components, accessibility |
| `navigation.md` | Screen inventory, navigation patterns, and flow diagrams |
| `data-model.md` | Canonical database schema — entities, fields, constraints, indexes |
| `api-contract.md` | Transport-layer API contract — endpoints, schemas, auth, versioning |
| `offline-sync.md` | Offline-first sync strategy — lifecycle, conflict resolution, concurrency |
| `feature-requirements.md` | Per-feature template: scope, requirements, acceptance criteria, dependencies |
| `feature-design.md` | Per-feature template: technical design, component structure, data models |
| `feature-tasks.md` | Per-feature template: task breakdown with effort estimates and dependencies |

## How to use these

1. **Copy the root-level templates first** — `PRODUCT.md`, `SPEC.md`, `ARCHITECTURE.md`, `ROADMAP.md` — and fill in the placeholders for your project. These set the foundation everything else references.
2. **Add the cross-cutting technical specs** as your stack requires them — `data-model.md` is almost always needed; `api-contract.md` and `offline-sync.md` only if your project has a backend or works offline.
3. **Add `design.md` and `navigation.md`** once you're ready to define the UI layer.
4. **For each feature**, create a folder (e.g. `specs/features/[feature-name]/`) and copy in the three feature templates, renaming them to `requirements.md`, `design.md`, and `tasks.md`.
5. **Fill in one document at a time.** Don't try to complete every placeholder in one pass — work through one document, get it reviewed (by yourself or a collaborator), then move to the next. This mirrors the "one document, one change, one session" principle the templates were built around.

### Suggested file layout

```
your-project/
├── PRODUCT.md
├── SPEC.md
├── ARCHITECTURE.md
├── ROADMAP.md
├── specs/
│   ├── design/
│   │   ├── design.md
│   │   └── navigation.md
│   ├── technical/
│   │   ├── data-model.md
│   │   ├── api-contract.md
│   │   └── offline-sync.md
│   └── features/
│       └── [feature-name]/
│           ├── requirements.md
│           ├── design.md
│           └── tasks.md
```

## Conventions baked into these templates

- **ID format:** `[TYPE]-[FEAT]-NN` — e.g. `RQ-ON-01`, `AC-ON-12`, `TSK-ON-05`, `BR-AC-04`. The two-letter feature abbreviation stays consistent across all three feature files.
- **Feature specs reference, never copy.** If a feature relies on a global business rule or schema field, link the ID — don't paste the rule text into the feature spec.
- **Derived values are never persisted.** Anything computable at query time (balances, progress percentages, totals) is computed, not stored — call this out explicitly in your data model.
- **Soft delete only.** No hard deletes in the data model or API layer — every entity carries a `deleted_at`.
- **Out-of-scope sections are mandatory.** Every requirements doc should say what it's explicitly *not* covering, and which phase (if any) picks it up.

## Notes

These templates assume a phased rollout (e.g. mobile-offline → backend+sync → web → cross-platform), but the structure holds up fine for a single-phase or single-platform project — just delete the phase scaffolding you don't need rather than leaving it as unused placeholders.

They're intentionally opinionated about *structure*, not technology — the examples lean Android/Kotlin/FastAPI/React because that's where they came from, but the document shapes apply to any stack.
