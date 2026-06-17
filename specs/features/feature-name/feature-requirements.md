# [Feature Name] — Requirements

**Version:** 0.0.1
**Status:** Draft
**Phase:** [1 (Android) / 2 (Backend) / 3 (Web)]
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Introduction

[One to three paragraphs describing the feature from the user's perspective. Answer:
- What does this feature do and why does it exist?
- When does the user encounter it?
- What is its relationship to the rest of the product?
- Any self-containment or lifecycle notes (e.g. "runs once on first launch and is never shown again unless…")]

---

## Scope Boundaries

### In Scope

- [Specific screen, flow, or behavior included in this phase]
- [Specific screen, flow, or behavior included in this phase]
- [Background/initialization behavior included]
- [State persistence mechanism included]
- [Platform and phase this spec covers]

### Out of Scope

- [Capability deferred to Phase N — reason]
- [Integration deferred to Phase N]
- [Platform not covered — note which spec covers it]
- [Feature variant explicitly excluded]

---

## Requirements

> Requirements are permanent once assigned. IDs follow the pattern `RQ-[FEAT]-NN` using a two-character feature abbreviation.
> Group requirements by screen or functional area. Each requirement is a single, testable behavioral statement.

### [Screen / Area 1, e.g. Feature Slides]

**RQ-[FT]-01 — [Short title]**
[Behavioral description. Be specific: what is the trigger, what must happen, what are the constraints. Reference design tokens and BR IDs where applicable. Avoid implementation specifics — those belong in the design doc.]

**RQ-[FT]-02 — [Short title]**
[Behavioral description.]

**RQ-[FT]-03 — [Short title, e.g. Visual layout]**
[Layout requirements if they are part of the product spec. E.g. "Each screen must display, from top to bottom: [element], [element], [element]."]

**RQ-[FT]-04 — [Short title, e.g. Content]**
[Content/copy requirements. Use a table if there are multiple variants:]

| Variant | [Column 1] | [Column 2] | [Column 3] |
|---|---|---|---|
| 1 | [value] | [value] | [value] |
| 2 | [value] | [value] | [value] |

**RQ-[FT]-05 — [Short title, e.g. Skip/dismiss behavior]**
[Behavioral description. Note what flag or state is NOT set when the user dismisses.]

**RQ-[FT]-06 — [Short title, e.g. Primary CTA behavior]**
[Behavioral description. Distinguish from skip if there is a secondary action.]

**RQ-[FT]-07 — [Short title, e.g. Suppression / re-show conditions]**
[When does this screen/flow NOT appear. What flag controls it and when is it set.]

**RQ-[FT]-08 — [Short title, e.g. Back navigation]**
[Back behavior per screen state. E.g. "On the first step, back exits the app. On subsequent steps, back navigates to the previous step."]

---

### [Screen / Area 2, e.g. Input Screen]

**RQ-[FT]-09 — [Short title, e.g. Display screen]**
[Screen display conditions. Visual transition from previous screen if applicable.]

**RQ-[FT]-10 — [Short title, e.g. Input field]**
[Field spec: placeholder, max length, min length, character restrictions, formatting, transparent/opaque background.]

**RQ-[FT]-11 — [Short title, e.g. CTA button state]**
[Button enabled/disabled conditions tied to field validation. What happens when the field is cleared after a valid entry.]

**RQ-[FT]-12 — [Short title, e.g. Persistence]**
[What is saved, when, where (e.g. SharedPreferences, Room, backend), and what keys/fields are used.]

**RQ-[FT]-13 — [Short title, e.g. Flag/state update]**
[When exactly is the completion flag or state set. Be precise — "on tap of Continue on [specific step]", not "on completion".]

---

### [Screen / Area 3, e.g. Entity Creation Form]

**RQ-[FT]-14 — [Short title, e.g. Display screen]**
[When is this screen shown, what is its relationship to the previous screen.]

**RQ-[FT]-15 — [Short title, e.g. Required fields]**
[List all required fields and their types. Reference the canonical entity definition in `data-model.md`.]

**RQ-[FT]-16 — [Short title, e.g. Optional fields]**
[Optional fields and when they appear (e.g. conditionally shown based on a type selection).]

**RQ-[FT]-17 — [Short title, e.g. Field validation]**
[Validation rules per field: min/max, allowed values, cross-field constraints. Reference BR IDs.]

**RQ-[FT]-18 — [Short title, e.g. Save button state]**
[All conditions that must be true for the Save button to be enabled. Be exhaustive.]

**RQ-[FT]-19 — [Short title, e.g. Save behavior]**
[What happens on a successful save: what is written, dialog/confirmation shown, navigation triggered.]

**RQ-[FT]-20 — [Short title, e.g. Skip / defer behavior]**
[Whether the user can skip the creation step and what happens if they do.]

---

### [Background / Initialization]

**RQ-[FT]-NN — [Short title, e.g. Database initialization]**
[Describe background tasks that run during this feature: what is created, in what order, and on what condition.]

**RQ-[FT]-NN — [Short title, e.g. Default data seeding]**
[Seeded records, their fields, and the source of truth for their content. Use a table if many records:]

| [Field 1] | [Field 2] | [Field 3] |
|---|---|---|
| [value] | [value] | [value] |

---

## Business Rules

> Reference rules from SPEC.md by ID. Never copy rule text here.

- BR-[XX]-NN: [one-line reminder of what the rule is — not the rule text itself]
- BR-[XX]-NN: [one-line reminder]

**Feature-specific rules:**

**BR-[FT]-01 — [Short title]**
[Rule statement. Format: "A [entity] must/must not [condition]."]

**BR-[FT]-02 — [Short title]**
[Rule statement.]

---

## Acceptance Criteria

> ACs are permanent once assigned. IDs follow the pattern `AC-[FEAT]-NN`.
> Each AC is a Given/When/Then scenario that can be executed as a manual test.

**AC-[FT]-01 — [Short title]**
Given [precondition],
When [action],
Then [observable outcome].

**AC-[FT]-02 — [Short title]**
Given [precondition],
When [action],
Then [observable outcome].

> Include ACs for: happy path, edge cases (empty state, max length, zero values), error cases, back navigation behavior, flag/state transitions, and destructive actions.

---

## Error Handling

| Scenario | Error Type | User-Facing Message | Recovery |
|---|---|---|---|
| [e.g. DB initialization failure] | [e.g. Full-screen error state] | "[e.g. Something went wrong. Please try again.]" | [e.g. Retry button re-runs initialization] |
| [e.g. Duplicate name] | [e.g. Inline field error] | "[e.g. A [entity] with this name already exists.]" | [e.g. User corrects the name field] |
| [e.g. Write failure] | [e.g. Inline error below Save] | "[e.g. Couldn't save. Please try again.]" | [e.g. User taps Save again] |
| [e.g. Field at character limit] | [e.g. Input silently stops] | None — field simply stops | [e.g. Informed by field behavior only] |
| [e.g. Amount exceeds maximum] | [e.g. Inline field error] | "[e.g. Maximum amount is $X.]" | [e.g. User corrects the amount] |

---

## Dependencies

| Dependency | Type | Notes |
|---|---|---|
| [e.g. Room database] | Internal | [e.g. [Entity] must be fully defined before this feature can be implemented] |
| [e.g. SharedPreferences / DataStore] | Internal | [e.g. `[flag_key]` and `[value_key]` keys] |
| [e.g. Parent entity] | Internal | [e.g. Must exist before child entity can be created (BR-[XX]-NN)] |
| [e.g. Font resource] | Design | [e.g. `[FontFamily]` defined in `core/ui/theme/Type.kt`] |
| [e.g. Illustration assets] | Design | [e.g. N illustration assets. Placeholders acceptable for Phase 1.] |
| [e.g. Design token] | Design | [e.g. `color.accent.primary` used as background for [screen]] |
| [e.g. Navigation component] | Internal | [e.g. Feature has its own NavHost. Navigation to [destination] via Intent.] |

---

## Platform: [Platform 2, e.g. Web] (Phase N)

[Describe how this feature differs on the next platform. Cover:]

- **[Area of difference]:** [How it changes. E.g. "Authentication replaces display name collection."]
- **[Area of similarity]:** [What stays the same. E.g. "Feature slides are the same three slides, adapted for web layout."]
- **[State management difference]:** [E.g. "No SharedPreferences — state derived from session presence."]
- **[API difference]:** [E.g. "Form submits to `POST /api/v1/[resource]` instead of writing directly to Room."]

Full [Platform 2] spec is defined at Phase N kickoff.

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
