---
description: Accessibility MUSTs for Vue MFE markup
globs: "**/*.{vue,html,jsx,tsx}"
alwaysApply: false
---

# Frontend Accessibility

Apply when editing component templates in MFE repos.

## Selectors and IDs

- **`data-test-id` for tests** — never use `id` or CSS classes as test selectors.
- **`id` only when referenced** — add an `id` only if another element or script needs it (`aria-labelledby`, `aria-controls`, programmatic focus, anchor links).
- Do NOT add `id` for future use or copy it from legacy markup without wiring it up.

## Labels and roles

- **Never `aria-label` on non-interactive elements** (`span`, `div`, `p`) — only on buttons, links, form controls, or elements with a naming ARIA role.
- **Do not duplicate visible text in `aria-label`** — visible text is the accessible name.
- **Decorative sub-labels** — `aria-hidden="true"` when the main label already identifies the control.
