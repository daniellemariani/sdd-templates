# Design System — [App Name]

**Version:** 0.0.1
**Status:** Draft
**Owner:** [Owner Name]
**Created at:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD

---

## Overview

This document defines the visual design system, UX principles, theming strategy, and cross-platform UI guidelines for [App Name].

The goal of the design system is to create a [describe the core emotional quality — e.g. calm, trustworthy, and highly readable] experience that scales consistently across [Platform 1], [Platform 2], and future clients.

This document acts as the canonical visual and UX reference for the product. All feature specifications, component implementations, and platform-specific UI layers must align with the principles and constraints defined here.

This document is intentionally implementation-agnostic. It defines visual and interaction standards, not platform-specific code.

---

## Related Documents

| Document | Purpose |
|---|---|
| SPEC.md | Global business rules and feature index |
| PRODUCT.md | Product vision and roadmap |
| ARCHITECTURE.md | Technical architecture and platform decisions |
| navigation.md | Navigation flows and screen relationships |

---

## Goals

- [Core visual/UX goal, e.g. Create a minimalist and professional experience]
- [Goal, e.g. Reduce cognitive load when viewing complex information]
- [Goal, e.g. Prioritize readability and information hierarchy over decoration]
- [Goal, e.g. Establish a consistent cross-platform visual identity]
- Define a reusable color system and typography scale
- Standardize spacing, elevation, and component behavior
- Ensure accessibility and dark mode parity from the beginning
- Prevent long-term design drift as features and platforms expand

---

## Non-Goals

- Define implementation-specific [Compose / React / Flutter] widgets
- Replace platform design systems ([Material 3 / HIG / etc.] remains the [Platform] foundation)
- Provide pixel-perfect mockups for every screen
- Define marketing website branding or promotional assets
- Define illustration systems or mascot/brand character guidelines

---

## Design Philosophy

[App Name] is designed around the concept of:

> [One short phrase that captures the core feeling — e.g. "Calm Financial Control" / "Clear, Confident Action"]

The product should help users feel [outcome — e.g. organized, informed, and in control] without creating unnecessary [friction — e.g. visual stress, cognitive load].

The app should feel:

- [Adjective, e.g. Stable]
- [Adjective, e.g. Trustworthy]
- [Adjective, e.g. Clean]
- [Adjective, e.g. Modern]
- [Adjective, e.g. Professional]

The app should avoid:

- [Anti-pattern, e.g. Aggressive aesthetics]
- [Anti-pattern, e.g. Excessive gradients and bright colors]
- [Anti-pattern, e.g. Visual clutter]
- [Anti-pattern, e.g. Overly playful motion]
- [Anti-pattern, e.g. Dense unreadable layouts]

The user experience should emphasize clarity, consistency, and long-term usability over novelty.

---

## Design Principles

### [Principle 1 — e.g. Clarity Over Decoration]

[One sentence defining the principle. E.g. "UI elements exist to improve understanding — visual decoration must never reduce readability or increase cognitive load."]

[One to two sentences of practical guidance for designers and implementers.]

---

### [Principle 2 — e.g. Neutral-First Design]

[One sentence defining the principle.]

Accent and [semantic / state] colors should be reserved for:

- [Specific use case, e.g. Actions]
- [Specific use case, e.g. Status indicators]
- [Specific use case, e.g. Warnings]
- [Specific use case, e.g. Charts]

---

### [Principle 3 — e.g. Information Hierarchy Through Spacing]

[One sentence defining the principle. E.g. "Whitespace and layout structure are preferred over heavy visual separators."]

[Practical guidance — e.g. "The UI should feel breathable even when displaying data-dense screens. Spacing consistency is mandatory across all platforms."]

---

### [Principle 4 — e.g. Trustworthy and Stable]

[One sentence defining the principle.]

Animations, transitions, and interactions should reinforce [stability / clarity] rather than entertainment.

---

### Cross-Platform Consistency

[Platform 1], [Platform 2], and future clients should feel like members of the same product family.

Platform-specific conventions may differ, but typography, colors, layout philosophy, spacing, semantic meaning, and component behavior must remain consistent.

---

## Visual Identity

### Overall Style

The visual style is [adjective list — e.g. minimalist, modern, professional, data-focused].

The UI should use:

- [Element — e.g. Soft elevated surfaces]
- [Element — e.g. Rounded cards]
- [Element — e.g. Restrained shadows]
- [Element — e.g. Spacious layouts]
- [Element — e.g. High-readability typography]
- [Element — e.g. Limited accent usage]

The UI should avoid:

- [Anti-pattern — e.g. Glassmorphism]
- [Anti-pattern — e.g. Heavy gradients]
- [Anti-pattern — e.g. Overly saturated palettes]
- [Anti-pattern — e.g. Decorative textures]

---

### Reference Applications

The following applications represent strong visual references for [App Name]:

- [App 1] — [what it does well, e.g. visual polish and premium calm aesthetic]
- [App 2] — [what it does well, e.g. information organization]
- [App 3] — [what it does well, e.g. dashboard composition]

These references are directional inspiration only. [App Name] maintains its own identity and implementation.

---

## Color System

### Color Philosophy

[One paragraph on the color philosophy. E.g. "The color system is intentionally restrained. Most of the interface uses neutral surfaces with accent colors reserved for meaningful states and interactions."]

[One sentence on why the primary accent color was chosen. E.g. "The primary accent uses a muted teal palette because it communicates trust and stability without the aggressiveness of bright greens."]

---

### Light Theme

| Role | Token | Hex |
|---|---|---|
| Background | `color.background.primary` | `#[hex]` |
| Surface / Card | `color.surface.card` | `#[hex]` |
| Surface Alt | `color.surface.alt` | `#[hex]` |
| Border | `color.border.default` | `#[hex]` |
| Primary Text | `color.text.primary` | `#[hex]` |
| Secondary Text | `color.text.secondary` | `#[hex]` |
| Primary Accent | `color.accent.primary` | `#[hex]` |
| On Primary Accent | `color.accent.on` | `#[hex]` |
| Success | `color.semantic.success` | `#[hex]` |
| Error | `color.semantic.error` | `#[hex]` |
| Warning | `color.semantic.warning` | `#[hex]` |
| Info | `color.semantic.info` | `#[hex]` |

---

### Dark Theme

| Role | Token | Hex |
|---|---|---|
| Background | `color.background.primary` | `#[hex]` |
| Surface / Card | `color.surface.card` | `#[hex]` |
| Surface Alt | `color.surface.alt` | `#[hex]` |
| Border | `color.border.default` | `#[hex]` |
| Primary Text | `color.text.primary` | `#[hex]` |
| Secondary Text | `color.text.secondary` | `#[hex]` |
| Primary Accent | `color.accent.primary` | `#[hex]` |
| On Primary Accent | `color.accent.on` | `#[hex]` |
| Success | `color.semantic.success` | `#[hex]` |
| Error | `color.semantic.error` | `#[hex]` |
| Warning | `color.semantic.warning` | `#[hex]` |
| Info | `color.semantic.info` | `#[hex]` |

---

### Dark Mode Strategy

- Dark mode is supported from Phase 1 and is not a future addition.
- Dark backgrounds use [strategy — e.g. layered neutral grays with progressively lighter surfaces].
- Accent hue remains in the same color family across light and dark — adapted for contrast, not replaced with a different hue.
- Semantic colors maintain their meaning in dark mode; only their shades are adjusted for accessibility.

---

### Color Independence Rule

Color alone must never be the sole signal communicating a meaningful state. Every color-coded state must also include at least one of:

| Secondary signal | Examples |
|---|---|
| Icon | [e.g. warning triangle, checkmark, error circle] |
| Label / text | [e.g. "Over budget", "On track", "Completed"] |
| Pattern or shape | [e.g. dashed border for a draft/inactive state] |

---

## Typography

### Display / Brand Font *(optional)*

> If the app uses a distinct brand font (separate from the UI font), document it here.

- **Font:** [Font name, e.g. Borel]
- **Usage:** [Where it appears — e.g. primary tab header only]
- **Size:** [e.g. 26sp]
- **Weight:** [e.g. Regular]
- **[Platform] registration:** [e.g. Add `borel_regular.ttf` to `res/font/`, declare `FontFamily` in `core/ui/theme/Type.kt`]
- **Constraints:** Never used in body text, form fields, or data tables.

---

### UI Font

- **Font:** [Font name, e.g. Inter]
- **Source:** [e.g. Google Fonts — variable font]
- **Weights used:** [e.g. 400 (Regular), 500 (Medium), 600 (SemiBold)]

---

### Typography Scale

| Role | Size | Weight | Line Height | Usage |
|---|---|---|---|---|
| Display | [e.g. 32sp] | [e.g. SemiBold] | [e.g. 1.2] | [e.g. Large hero numbers] |
| Heading 1 | [e.g. 24sp] | [e.g. SemiBold] | [e.g. 1.3] | [e.g. Screen titles] |
| Heading 2 | [e.g. 20sp] | [e.g. SemiBold] | [e.g. 1.3] | [e.g. Section headers] |
| Heading 3 | [e.g. 18sp] | [e.g. Medium] | [e.g. 1.4] | [e.g. Card headers] |
| Body | [e.g. 16sp] | [e.g. Regular] | [e.g. 1.5] | [e.g. Primary content] |
| Body Small | [e.g. 14sp] | [e.g. Regular] | [e.g. 1.5] | [e.g. Secondary content] |
| Label | [e.g. 12sp] | [e.g. Medium] | [e.g. 1.4] | [e.g. Tags, chips, badges] |
| Caption | [e.g. 11sp] | [e.g. Regular] | [e.g. 1.4] | [e.g. Timestamps, hints] |

> Use `sp` units on Android to respect system font size scaling. All layouts must remain functional at up to 200% system font scale.

---

### Number / Amount Formatting

| Value type | Format |
|---|---|
| Currency (positive) | [e.g. `$1,234.56`] |
| Currency (negative) | [e.g. `−$1,234.56` in `color.semantic.error`] |
| Currency (zero) | [e.g. `$0.00`] |
| Percentage | [e.g. `68%`] |
| Large amounts | [e.g. `$1,200` not `$1200`] |

Currency amounts should always right-align in tables and lists.

---

## Spacing

### Spacing Scale

| Token | Value | Usage |
|---|---|---|
| `spacing.xxs` | 2dp | Micro-gaps between inline elements |
| `spacing.xs` | 4dp | Tight internal padding |
| `spacing.sm` | 8dp | Component internal padding |
| `spacing.md` | 16dp | Standard layout padding, card content padding |
| `spacing.lg` | 24dp | Section gaps, large card padding |
| `spacing.xl` | 32dp | Screen-level vertical rhythm |
| `spacing.xxl` | 48dp | Large separators, hero spacing |

---

## Shape / Radius

| Token | Value | Usage |
|---|---|---|
| `radius.sm` | [e.g. 6dp] | Chips, tags, badges |
| `radius.md` | [e.g. 10dp] | Input fields, small cards |
| `radius.lg` | [e.g. 16dp] | Primary cards, list containers |
| `radius.xl` | [e.g. 24dp] | Bottom sheets, dialogs |

---

## Elevation

| Token | Value | Usage |
|---|---|---|
| `elevation.none` | 0dp | Flat surfaces, list items |
| `elevation.sm` | 1dp | Cards, subtle lift |
| `elevation.md` | 4dp | Bottom sheets, menus |
| `elevation.lg` | 8dp | Dialogs, modals |

---

## Component Patterns

### Cards

Cards are the primary container for grouped information. Use `color.surface.card`, `radius.lg`, and `elevation.sm`. Card padding is `spacing.md` on all sides.

[Add any card variants your app uses — e.g. summary card, list card, status card.]

---

### [Reusable Component 1, e.g. StatusPill / CategoryPill]

**Purpose:** [One-line description.]

**Anatomy:** [e.g. Icon + label / Emoji + text]

**Color rules:** [e.g. Text and icon at full opacity; background derived at render time as hex color at 15% alpha — only one color value is stored.]

**Shape:** `radius.sm`

**Typography:** `Label` scale

**Padding:** `spacing.xs` vertical / `spacing.sm` horizontal

**Behavior:** [e.g. Display-only — not interactive]

**Accessibility:** [e.g. Icon is decorative and hidden from screen readers; label is the accessible name]

---

### Progress Indicators

[Describe how progress bars communicate status thresholds. Example:]

| Threshold | Color |
|---|---|
| 0–84% | `color.accent.primary` |
| 85–99% | `color.semantic.warning` |
| 100%+ | `color.semantic.error` |

The threshold transitions are sharp (not gradual). Color changes at exactly the defined breakpoints.

---

### Iconography

- **Icon library:** [e.g. Material Symbols / Tabler Icons / Phosphor]
- **Style:** [e.g. Outlined — never filled, except for the active bottom nav tab]
- **Sizes:** [e.g. 24dp for navigation and action icons; 20dp for inline/list icons]
- **Usage rules:**
  - Icons always accompany a label or have an accessible content description
  - Do not use icons as decorative elements without semantic meaning
  - [Add any app-specific icon conventions]

---

### Empty States

Every list screen must define an empty state. Anatomy: a lightweight illustration placeholder (or icon), a short headline, one sentence of subtext, and a primary CTA button.

Empty states should feel encouraging, not apologetic. They should point directly to the action that resolves the empty state.

See `navigation.md` for the two-level empty state pattern (dependency not met vs. no data yet).

---

### Loading States

Loading states must be subtle and preserve layout stability. Skeleton screens are preferred over spinners for list content — they prevent layout jump when data loads.

Skeletons use `color.surface.alt` as the base with a shimmer animation in the same neutral family. Avoid full-screen loading states — load content progressively where possible.

---

### Sync Indicators

Sync status indicators must remain low-noise and non-intrusive. The sync system is background-oriented and must not interrupt primary workflows.

| Sync state | Indicator style |
|---|---|
| Syncing (background) | Subtle spinner or no indicator |
| Last synced (idle) | Caption-style timestamp, secondary text color |
| Sync failed | Warning-colored icon + brief label |
| Conflict detected | Warning-colored banner or badge, requires user action |
| Prolonged offline | Passive offline badge, non-blocking |

---

## Motion & Animation

### Motion Philosophy

Animations support comprehension and continuity. Motion must never feel playful or distracting. The app should feel responsive, stable, predictable, and calm.

### Motion Guidelines

Recommended:
- Short transitions ([e.g. 150–250ms])
- Subtle fades for state changes
- Gentle slide transitions for screen navigation
- Minimal scale effects for confirmation feedback

Avoid:
- Bounce or spring physics
- Large parallax effects
- Long-duration transitions (> [e.g. 400ms])
- Decorative motion unrelated to user intent
- Animations that block access to content

---

## Accessibility

Accessibility is a first-class requirement.

### Contrast

All text and critical UI elements must meet **WCAG AA** minimum contrast requirements:
- Normal text: 4.5:1 contrast ratio minimum
- Large text (≥ 18sp regular or ≥ 14sp bold): 3:1 minimum
- UI components and graphical elements: 3:1 minimum

### Touch Targets

All interactive elements must meet the minimum touch target size defined in `SPEC.md` (NFR-AC-01):
- Minimum: 48dp × 48dp on all mobile platforms
- Visual size may be smaller; the touch target area must meet the minimum

### Font Scaling

All typography must use `sp` units on [Platform] to respect system font size scaling (NFR-AC-02). Layouts must remain functional and readable at up to 200% system font scale without truncation or overlap.

### Color Independence

Color alone must never communicate meaning. See the Color Independence Rule section above.

### Screen Reader Compatibility

All interactive elements require descriptive labels. Complex UI elements (e.g. progress bars, summary cards) must be announced with full context — a screen reader should announce "[Entity], [value], [status]" not just the raw number.

---

## Platform Adaptation

### [Platform 1, e.g. Android]

[Platform 1] uses **[Platform design system, e.g. Material 3]** as the foundational design system. [App Name] customizes the [design system] color scheme, typography, shape, and elevation tokens while preserving [platform]-native interaction expectations.

The [Material 3 / platform] token mapping to [App Name] tokens:

| Platform Role | [App Name] Token |
|---|---|
| `[platform_primary]` | `color.accent.primary` |
| `[platform_background]` | `color.background.primary` |
| `[platform_surface]` | `color.surface.card` |
| `[platform_error]` | `color.semantic.error` |
| [Add all relevant platform token mappings] | |

---

### [Platform 2, e.g. Web]

The web platform adapts the same design language to larger layouts using the same token values expressed as CSS custom properties. [UI library, e.g. shadcn/ui] components are customized to match [App Name] tokens via [Tailwind / CSS config].

The web UI must preserve visual consistency with mobile, increase information density appropriately for larger viewports, and maintain consistent semantic meaning across all states.

---

### [Platform 3, e.g. Flutter / KMP] *(Phase 4)*

Future [Platform 3] implementations must preserve the typography hierarchy, color system, component behavior, motion philosophy, spacing rules, and accessibility standards defined here. Platform differences are acceptable as long as the overall product identity remains consistent.

---

## Design Tokens

The token naming strategy below is the canonical reference for implementation. Platform-specific implementations must map to these token names.

### Color Tokens

```
color.background.primary
color.surface.card
color.surface.alt
color.border.default
color.text.primary
color.text.secondary
color.accent.primary
color.accent.on
color.semantic.success
color.semantic.error
color.semantic.warning
color.semantic.info
```

### Spacing Tokens

```
spacing.xxs   — 2dp
spacing.xs    — 4dp
spacing.sm    — 8dp
spacing.md    — 16dp
spacing.lg    — 24dp
spacing.xl    — 32dp
spacing.xxl   — 48dp
```

### Radius Tokens

```
radius.sm     — [value]dp   ([usage])
radius.md     — [value]dp   ([usage])
radius.lg     — [value]dp   ([usage])
radius.xl     — [value]dp   ([usage])
```

### Elevation Tokens

```
elevation.none   — 0dp
elevation.sm     — [value]dp   ([usage])
elevation.md     — [value]dp   ([usage])
elevation.lg     — [value]dp   ([usage])
```

---

## Future Considerations

The design system is expected to evolve alongside the product.

Potential future additions:
- Dedicated component specification document
- Chart visualization standards and color palette for multi-series charts
- Illustration or icon spot-art guidelines for empty states
- Motion timing token definitions
- Responsive web breakpoint definitions (Phase 3 kickoff)
- Advanced accessibility auditing and WCAG AAA targets
- [App-specific future consideration]

Future additions must remain consistent with the core philosophy defined in this document.

---

## Guiding Principle

[One to three sentences that capture the spirit of the whole design system. Written to be memorable and inspirational — something designers and engineers can return to when making judgment calls.]

---

## Changelog

| Version | Date | Author | Notes |
|---|---|---|---|
| 0.0.1 | YYYY-MM-DD | [Author] | Initial draft |
