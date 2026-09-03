---
name: review-changes
description: Review frontend code changes for config, a11y, testing, and Vue conventions. Use when reviewing pull requests, diffs, or when the user asks for a code review.
---

# Review Changes

Review workflow for frontend PRs and local diffs — config, a11y, tests, Vue conventions, and PR hygiene.

Before reviewing, apply all relevant rules in `rules/` (especially `vue-feature-architecture.md`, `tailwind.md`, `frontend-accessibility.md`, `frontend-testing.md`).

## Workflow

1. Read the diff and description — identify purpose, scope, and ticket.
2. Check **one PR = one purpose** — flag unrelated changes (dev-server, deps, refactors).
3. Review against the checklist below and rule MUSTs.
4. Format feedback by severity:
   - 🔴 **Critical** — must fix before merge
   - 🟡 **Suggestion** — consider improving
   - 🟢 **Nice to have** — optional

## Quick checklist

- [ ] Every user-facing string is in config or from the API — none hardcoded in templates.
- [ ] Labels are natural case; CSS handles uppercase.
- [ ] No `aria-label` on non-interactive elements or duplicating visible text.
- [ ] Sub-labels / decorative text carry `aria-hidden="true"`.
- [ ] Tests use `shallowMount` (unless integration warrants `mount`) + `data-test-id` queries.
- [ ] Jest mocks use `.mockImplementation()`, not reassignment.
- [ ] No `eslint-disable` for a fixable rule.
- [ ] No unrelated changes bundled (webpack, dev-server, unrelated refactors).
- [ ] SFC convention matches the repo (Options API unless repo has migrated).
- [ ] JSDoc on public props / composables describes shape, not just `{Object}`.
- [ ] Single source of truth for arrays / enums referenced twice.
- [ ] `git mv` used for legacy renames to preserve history.
- [ ] Spec/filenames include ticket code when scoped to one ticket.
- [ ] Feature module boundaries respected (`pages/containers/components/services/logic/store`).
- [ ] Key-case conversion only in service layer, not components or `logic/`.

## Config vs code

- Config values must legitimately vary per environment — otherwise use FE constants.
- Only ship config keys for features that render; add labels in the same PR as the feature.
- Server-driven API text renders as-is — no client mapping table.
- Guard optional blocks against partial config (all required fields).

## Testing

- Outermost `describe` matches component/service name (no file extension, no Jira ticket names).
- No assertions on `wrapper.vm` internal state.
- Each `it()` tests one behavior; edge cases and error paths covered.
- HTTP tests verify call args and post-response DOM state.
- No dedicated config schema tests — component tests already cover missing keys.
- Prefer integration tests only when child behavior is under test.
- For test MUSTs, apply `rules/frontend-testing.md`.

## Component design & reuse

- Generic components at app level with domain-agnostic APIs.
- Reuse primitives; differentiate in composition — no audience `variant` props on shared components.

## Documentation (specs & shared docs)

- No person attribution — use "Approach A / Approach B".
- Structural claims cite `file:line`.
- No `⚠️` placeholders in final specs — commit to a value or track in Jira.

## PR discipline

- Match naming/style of siblings; `-test.js` not `.spec.js`; follow repo import order.
