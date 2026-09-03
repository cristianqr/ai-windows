---
name: pr-review
description: Review frontend PRs for config, a11y, testing, and Vue conventions. Use when reviewing pull requests, code changes, or when the user asks for a code review.
---

# Frontend PR Review

Before reviewing, apply all relevant rules in `rules/` (especially `tailwind.mdc`, `frontend-testing.mdc`).

## Workflow

1. Read the PR diff and description — identify purpose, scope, and ticket.
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
- [ ] Tests use `shallowMount` (unless integration warrants `mount`) + `data-test-id` queries.
- [ ] Jest mocks use `.mockImplementation()`, not reassignment.
- [ ] No `eslint-disable` for a fixable rule.
- [ ] No unrelated changes bundled (webpack, dev-server, unrelated refactors).
- [ ] SFC convention matches the repo (Options API unless repo has migrated).
- [ ] JSDoc on public props / composables describes shape, not just `{Object}`.
- [ ] Single source of truth for arrays / enums referenced twice.
- [ ] `git mv` used for legacy renames to preserve history.
- [ ] Spec/filenames include ticket code when scoped to one ticket.

## Config vs code

- Config values must legitimately vary per environment — otherwise use FE constants.
- Only ship config keys for features that render; add labels in the same PR as the feature.
- Server-driven API text renders as-is — no client mapping table.
- Guard optional blocks against partial config (all required fields).

## Testing

- No dedicated config schema tests — component tests already cover missing keys.
- Prefer integration tests only when child behavior is under test.

## Component design & reuse

- Generic components at app level with domain-agnostic APIs.
- Reuse primitives; differentiate in composition — no audience `variant` props on shared components.

## Documentation (specs & shared docs)

- No person attribution — use "Approach A / Approach B".
- Structural claims cite `file:line`.
- No `⚠️` placeholders in final specs — commit to a value or track in Jira.

## PR discipline

- Match naming/style of siblings; `.spec.js` not  `-test.js`; follow repo import order.
