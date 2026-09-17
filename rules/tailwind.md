---
description: Tailwind CSS and design-system styling conventions
globs: "**/*.{vue,html,jsx,tsx}"
alwaysApply: false
---

# Tailwind & Design System Styling

Applies when editing component markup in any frontend repo that uses Tailwind and the shared design system.

## Tailwind utilities

- Use **Tailwind utility classes** for all new and edited markup — not custom CSS.
- Prefer existing design-system utilities before inventing combinations: `mx-auto`, `heading-title`, `btn-primary`, `btn-secondary`, `btn-quaternary`, `bg-shade`, etc. Match siblings in the feature folder.
- Prefer Tailwind utilities over component-scoped CSS (`<style>` blocks, CSS modules, styled components) in new and edited markup.

## No default-value utilities

Do **not** add a Tailwind class that restates a CSS default or a value the element already inherits. A class is justified only when it **changes** what the browser or a parent already applies.

- Check the element type and its ancestors before adding a utility.
- Sibling styles do not inherit. A `font-bold` label does **not** make a sibling value `font-bold`.
- Drop the `class` attribute entirely when no utilities remain.

```html
<!-- ❌ restates defaults / inheritance -->
<div class="font-normal">{{ value }}</div>
<div class="block text-left">{{ value }}</div>

<!-- ✅ no class — div is already block, left-aligned, font-weight: 400 -->
<div>{{ value }}</div>

<!-- ✅ class changes something -->
<div class="font-bold">{{ label }}</div>
<div class="truncate">{{ title }}</div>
```

Common defaults to skip unless a parent changed them: `font-normal`, `text-left`, `block` on a `<div>`, `flex-row` on `flex`, `static`, `m-0` on a `<div>`.

## Sizing — use `rem`, not `px`

- Prefer existing Tailwind/design-system size utilities first (`text-sm`, `text-lg`, `p-4`, `tracking-wide`, etc.).
- When no utility exists and an **arbitrary value** is required, use **`rem`**, not `px`.
  - ✅ `md:text-[2.25rem]`
  - ❌ `md:text-[36px]`
- Convert px → rem at **`1rem = 16px`** (e.g. 36px → `2.25rem`, 24px → `1.5rem`).
- Applies to arbitrary utilities: `text-[…]`, `w-[…]`, `h-[…]`, `gap-[…]`, `p-[…]`, `m-[…]`, etc.

**Exception — sub-1px letter-spacing:** when the design spec gives `letter-spacing` **below 1px** (e.g. `0.14px`), keep **`px`** in the arbitrary value. Converting to `rem` produces unreadable decimals (`0.14px` → `0.00875rem`) with no practical benefit for sub-pixel tracking.

  - ✅ `tracking-[0.14px]`
  - ❌ `tracking-[0.00875rem]`
  - Values **≥ 1px** still use `rem` (e.g. `1px` → `tracking-[0.0625rem]` or `tracking-[1px]` is acceptable).

## CSS Modules (when used)

- Class names are **kebab-case** (e.g. `.summary-panel`); access via `$style['summary-panel']`, not
  camelCase property access (`$style.summaryPanel`).
- Prefer Tailwind utilities first — see above; CSS modules are for the rare case Tailwind can't
  cover.

## Selectors — not for styling

- **`data-test-id`** — test selectors only (`data-test-id="submit-button"`), never for styling.
- Do not rely on CSS classes or `id` for tests.

## Forbidden

- No scoped CSS, SCSS modules, or BEM in new/edited components when Tailwind covers the styling need.
- Do not proactively rewrite untouched pre-existing utility/class patterns — only change markup you are actually editing.

## Formatting

Let Prettier handle Tailwind class wrapping — do not hand-reformat unrelated markup.
