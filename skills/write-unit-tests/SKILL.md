---
name: write-unit-tests
description: Write frontend unit tests. Use when writing or updating *-test.js files — not when reviewing tests or only running the test suite. Conventions are in rules/frontend-testing.md.
---

# Write Unit Tests

Workflow for **authoring** frontend unit tests — not for reviewing tests or running the suite alone.

Apply all MUST conventions in `rules/frontend-testing.md` (auto-loaded on `**/*-test.js`).

For repo-specific setup (Jest config, mount helpers, mocks), follow patterns in the repo under test — check `package.json`, `jest.config.js`, and sibling `__tests__/` files.

## Workflow

1. Read the component or service under test — identify user-visible behaviors, not implementation details.
2. List behaviors to cover: happy path, edge cases, error states, async flows.
3. Arrange mocks (services, config, child components) before mounting.
4. Define selector helpers and a shared `wrapper` in the outermost `describe` (see `rules/frontend-testing-examples.md`).
5. Write one `it()` per behavior using AAA structure.
6. Trigger interactions via selector helpers or `[data-test-id="…"]` — never call `wrapper.vm` handlers directly.
7. For API flows: assert the call **and** await async resolution, then assert DOM updates.
8. After writing, verify locally: `npm run test <test-file-name>`.

## BDD examples

See `rules/frontend-testing-examples.md`.

## Not in scope

- **Reviewing tests** — use `skills/review-changes/SKILL.md` and `rules/frontend-testing.md`.
- **Running tests only** — use `npm run test` directly or follow `skills/implement-changes/SKILL.md` verification.
