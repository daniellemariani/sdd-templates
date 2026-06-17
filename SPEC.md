# [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

## Overview

[One paragraph describing what the app does, who it's for, and how it's delivered. This is the canonical source for the app name. All other spec documents reference the name generically (e.g. "[App Name]") and defer to this definition.]

**App name:** [App Name]

---

## Glossary

| Term | Definition |
|---|---|
| [Term 1] | [Definition] |
| [Term 2] | [Definition] |

> Add all domain-specific nouns here before writing requirements. Every term used in specs should be defined exactly once in this table.

---

## User Roles

### Phase 1
- **[Role]** — [description, e.g. single user, full access, no authentication required]

### Phase 2+

> Describe role-based access model here once multi-user support is introduced.

| Action | OWNER | ADMIN | MEMBER | VIEWER |
|---|---|---|---|---|
| [Action 1] | ✓ | ✓ | ✓ | ✓ |
| [Action 2] | ✓ | ✓ | ✓ | ✗ |
| [Action 3] | ✓ | ✓ | ✗ | ✗ |
| [Action 4] | ✓ | ✗ | ✗ | ✗ |

---

## Feature Index

### Phase 1 — [Platform, e.g. Android Offline]

| Feature | Spec | Description | Status |
|---|---|---|---|
| [Feature Name] | specs/features/[feature]/requirements.md | [One-line description] | Not Started |

### Phase 2 — [Platform, e.g. Backend + Sync]

| Feature | Spec | Description | Status |
|---|---|---|---|
| [Feature Name] | specs/features/[feature]/requirements.md | [One-line description] | Not Started |

### Phase 3 — [Platform, e.g. Web]

| Feature | Spec | Description | Status |
|---|---|---|---|
| [Feature Name] | specs/features/[feature]/requirements.md | [One-line description] | Not Started |

---

## Data Model Summary

| Entity | Key Fields | Notes |
|---|---|---|
| [Entity] | id, [field1], [field2], created_at, updated_at | [Any notable behavior] |

> Full schema lives in specs/technical/data-model.md. This table is a high-level index only.

---

## Business Rules

> Rules are permanent once assigned. IDs follow the pattern BR-[FEAT]-NN using a two-character feature abbreviation.

### [Domain, e.g. Budgets]
- BR-[XX]-01: [Rule statement]
- BR-[XX]-02: [Rule statement]

### [Domain, e.g. Accounts]
- BR-[XX]-01: [Rule statement]

---

## Global Non-Functional Requirements

### Performance
- NFR-PE-01: [e.g. All screens must render initial state in under 300ms]
- NFR-PE-02: [e.g. Database queries must complete in under 100ms for up to N years of data]

### Offline
- NFR-OF-01: [e.g. All Phase 1 features must work 100% offline]

### Accessibility
- NFR-AC-01: [e.g. Minimum touch target size of 48x48dp on all interactive elements]
- NFR-AC-02: [e.g. All UI must support system font size scaling]

### Data Safety
- NFR-DS-01: [e.g. No user data is transmitted to any external service in Phase 1]

---

## Out of Scope

- [Feature or capability explicitly excluded]
- [Feature deferred to a later phase — note the phase]

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
| ARCHITECTURE.md | Technical decisions and stack |
| CONTEXT.md | Session continuity and current state |
| specs/technical/data-model.md | Full database schema |
| specs/technical/api-contract.md | API endpoint definitions |
