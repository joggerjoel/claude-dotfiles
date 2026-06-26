---
name: uiverse
description: Source small, copy-paste UI elements from Uiverse (uiverse.io — the largest open-source UI library, 7000+ community elements as HTML/CSS, Tailwind, React, or Figma) when a task needs an individual styled primitive — a button, loader/spinner, toggle switch, checkbox, radio, input, card, tooltip, or background pattern — rather than authoring it from scratch. Framework-agnostic, so use it for any stack (HTML, Vue, Svelte, React, Tailwind). Auto-invoke when sourcing a specific small UI element; skip when the project design system already supplies it, or when the need is a large animated React component (use react-bits) or full layout/page.
---

# Uiverse

A library-sourcing skill for **small UI primitives**. When a task calls for a single nicely-styled element — a fancy button, a loader, a toggle — check Uiverse before hand-coding the CSS.

- **Site:** [uiverse.io](https://uiverse.io) · browse: [uiverse.io/elements](https://uiverse.io/elements)
- **Repo:** [github.com/uiverse-io/galaxy](https://github.com/uiverse-io/galaxy) (~11k★)
- **License:** **MIT** — free to use, modify, and ship in any project (commercial included), no attribution required.
- **Scale:** 7000+ community-made elements, growing constantly.

## What it offers

Each element page lets you **copy as HTML/CSS, Tailwind, React, or Figma** — pick the format that matches the project. Elements are self-contained (markup + styles), so they drop into any stack.

**Element categories:** Buttons · Checkboxes · Toggle Switches · Cards · Loaders · Spinners · Inputs · Radio Buttons · Forms · Tooltips · Patterns (backgrounds).

## When to invoke

Auto-trigger when the task is to produce or upgrade a **single small element**:

- "Make a nicer button / a fancy submit button / a gradient CTA"
- A loading spinner, skeleton, or animated loader
- A toggle switch, custom checkbox, custom radio, styled input
- A small card, tooltip, or decorative background pattern
- Any "this button/input/loader looks plain, make it pop" request — on **any** stack

## When NOT to invoke

- The project design system / `@*/ui` package already provides the element — extend that, don't introduce a parallel source
- The need is a **large animated React component, animated text, or full-page background** → use `react-bits` instead
- A whole layout, page, or multi-part component → use `frontend-design` / `ui-ux-pro-max`
- A trivial element you'd style in 2–3 lines — don't copy in someone else's CSS for it
- Accessibility-critical inputs: Uiverse elements are visual-first and **often skip a11y** (focus states, ARIA, keyboard, label association). Audit and fix before shipping — see below.

## Using it well (composes with design-taste)

1. **Copy in the matching format** — Tailwind elements for Tailwind projects, React for React, plain HTML/CSS otherwise. Don't paste raw CSS into a Tailwind codebase if a TW variant exists.
2. **Namespace / dedupe the CSS** — community CSS uses generic class names (`.button`, `.card`) and can collide. Scope it (CSS module, unique class, or convert to your token system) before adding.
3. **Re-tokenize colors** — replace hardcoded hex/gradients with the project's design tokens so the element matches the palette (`color-strategy` rules apply). A pasted purple-gradient button is classic AI slop if left as-is.
4. **Audit accessibility** — add focus-visible states, real `<label>`/`for` associations, ARIA, keyboard handling, and `prefers-reduced-motion` handling. Many Uiverse elements lack these.
5. **Tune the motion with `design-taste`** — durations < 300ms for UI, `ease-out` not `ease-in`, animate only `transform`/`opacity`, gate hover behind `@media (hover:hover)`.
6. **One flourish per surface** — don't combine five animated Uiverse elements on one screen.

## Composition with other skills

| Skill                    | Role                                                            |
| ------------------------ | --------------------------------------------------------------- |
| `uiverse` (this)         | **Source** small copy-paste UI primitives (any stack)           |
| `react-bits`             | **Source** large animated React components / text / backgrounds |
| `design-taste`           | **Tune** the motion + reject slop; inspiration sources          |
| `color-strategy`         | Re-tokenize the pasted element's colors                         |
| `ui-ux-pro-max`          | General UI/UX vocabulary & layout                               |
| project `/design-system` | Brand tokens & canonical components (takes precedence)          |

**Pick the source:** small primitive, any stack → `uiverse`. Large animated React component / hero text / animated background → `react-bits`. Whole layout or bespoke component → `frontend-design`.

## Maintenance

- Verify category list / counts against [uiverse.io/elements](https://uiverse.io/elements) if it drifts.
- License confirmed MIT via `gh api repos/uiverse-io/galaxy --jq .license.spdx_id`.
