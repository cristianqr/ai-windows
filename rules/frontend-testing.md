---
description: Unit test conventions for frontend test files (AAA, BDD, selectors, mocking)
globs: "**/*-test.js"
alwaysApply: false
---

# Frontend Unit Test Conventions

Apply whenever writing or editing `*-test.js` files in MFE repos.

## MUST practices

1. **Behavior over implementation** — assert what the component does from the user's perspective (inputs, outputs, emitted events, DOM changes). Do NOT test internal data properties or private methods.
2. **Reliable selectors** — query DOM via **`data-test-id`**, not CSS classes, `id`, or complex DOM paths. Components under test MUST expose `data-test-id` on elements with conditional rendering or dynamic content.
   - Define **selector helpers** inside the outermost component `describe`, alongside a shared `let wrapper`.
   - Selectors MUST be functions that call `wrapper.find('[data-test-id="…"]')` so they target the current mounted instance.
   - Mount helpers MUST assign to `wrapper` (`wrapper = mount(...)`). Nested `describe`/`beforeEach` may reassign `wrapper` when a scenario needs a different mount.
   - ❌ Module-level selector strings with ad hoc local `wrapper` variables in every test.
   - ❌ CSS classes, element IDs, or DOM structure when a `data-test-id` exists.
3. **AAA (Arrange, Act, Assert)** — structure every test: arrange state and mocks, act on the component, assert the outcome.
4. **Isolation via mocks** — mock complex child components, API calls, and external global services.
5. **Single responsibility per test** — each `it()` block MUST test one specific behavior. Avoid monolithic tests with dozens of assertions.
6. **No logic or internal state in tests** — no conditionals (`if`/`switch`) or loops (`for`/`while`) in test code. **Never assert internal component state** (e.g. do NOT use `expect(wrapper.vm.isAdminUser).toBe(true)`). Assert side-effects: DOM updates, emitted events, mocked function calls.
7. **Mock only what is necessary** — prefer real implementations for pure utilities and simple/fast child components when over-mocking would mask integration issues.
8. **Test edge cases and errors** — cover null inputs, thrown API exceptions, empty lists, and missing configuration flags, not just the happy path.
9. **Meaningful coverage over quantity** — prioritize critical user flows over trivial tests to hit 100% line coverage.
10. **Descriptive naming** — outermost `describe` MUST match the component or service name **without file extension** (e.g. `describe('UserComponent', ...)`). Inner `describe`/`it` blocks MUST form readable sentences. **Never** name inner `describe` blocks after Jira tickets or migration names.
11. **BDD structure** — group related cases under clear context blocks (e.g. `describe('when the user is logged in')`).
12. **Avoid redundant nesting** — do not repeat the component name in inner describes. Do not wrap required props in a context block when the component cannot exist without that prop.
13. **Trigger events via the DOM** — never call handler methods directly on the instance.
    - ❌ `wrapper.vm.handleSubmit()`
    - ✅ `submitButton().trigger('click')` — use selector helpers when defined (see item 2).
14. **Assert HTTP requests by call and parameters** — use `toHaveBeenCalledWith` with `expect.objectContaining(...)` to avoid over-specifying payloads.
15. **Assert DOM state after HTTP responses** — after async actions, `await flushPromises()` or `await nextTick()`, then assert the DOM reflects returned data. Verifying the call alone is insufficient.
16. **Prefer `shallowMount`** unless the test asserts a child component's behavior — use `mount` only for genuine integration wiring.
17. **Jest mocks — use `.mockImplementation()`** — do not reassign a mocked export (`foo.bar = jest.fn(...)` breaks `clearAllMocks` and call inspection).
    - ✅ `context.getConfigById.mockImplementation((id) => ...)`
    - ❌ `context.getConfigById = jest.fn((id) => ...)`

## Design System Components — do NOT mock manually

Shared design-system (`sb-*`) components are typically already globally stubbed in the repo's
test setup file (e.g. `test.setup.js`). Do **not** re-declare stubs for them inside individual spec
files — doing so is redundant and can cause conflicts. Check the repo's test setup before adding a
local stub; a common baseline looks like:

| Component | Props | Emits |
|---|---|---|
| `sb-badge` | `variation` | — |
| `sb-button` | — | — |
| `sb-panel` | `variation` | — |
| `sb-modal` | `active` | `close` |
| `sb-select` | `options`, `modelValue` | `update:modelValue` |
| `sb-text-input` | `value` | `update:value` |
| `sb-paginator` | `page`, `rows`, `totalRecords`, `rowsPerPageOptions` | `page` |
| `sb-tree-table` | `value` | — |
| `sb-page` | — | — |
| `sb-data-table` | `data`, `columnDefinitions` | `api-sort` |
| `sb-multi-select` | `options`, `modelValue` | `update:modelValue` |
| `sb-page-header` | `title`, `subTitle`, `breadcrumbs` | — |

Also commonly available globally: a mocked toast service (`info`/`success`/`error`/`warning`/`clear`
as `jest.fn()`), an `$t` i18n mock, and any other globally-provided service mocks the repo defines.

If a specific test needs to override a stub's template or props (e.g. to expose a named slot),
override only that component locally in the test's `stubs` option.

## Running tests locally

Use the CI-equivalent command:

```bash
npm run test <test-file-name>
```

For BDD naming, selector helpers, and HTTP assertion examples, see `rules/frontend-testing-examples.md`.
