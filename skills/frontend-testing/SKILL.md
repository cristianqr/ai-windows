---
name: frontend-testing-methodology
description: Workflow for writing and reviewing frontend unit tests. Use when writing, updating, or reviewing *-test.js files. Conventions are in rules/frontend-testing.md.
---

# Frontend Testing Workflow

Before writing or reviewing tests, apply all MUST conventions in `rules/frontend-testing.md` (auto-loaded on `**/*-test.js`).

For repo-specific setup (Jest config, mount helpers, mocks), follow patterns in the repo under test — check `package.json`, `jest.config.js`, and sibling `__tests__/` files.

## Writing tests

1. Read the component or service under test — identify user-visible behaviors, not implementation details.
2. List behaviors to cover: happy path, edge cases, error states, async flows.
3. Arrange mocks (services, config, child components) before mounting.
4. Define selector helpers and a shared `wrapper` in the outermost `describe` (see `rules/frontend-testing-examples.md`).
5. Write one `it()` per behavior using AAA structure.
6. Trigger interactions via selector helpers or `[data-test-id="…"]` — never call `wrapper.vm` handlers directly.
7. For API flows: assert the call **and** await async resolution, then assert DOM updates.
8. Run locally: `npm run test <test-file-name>`.

## Reviewing tests

- [ ] Outermost `describe` matches component/service name (no file extension, no Jira ticket names).
- [ ] No assertions on `wrapper.vm` internal state.
- [ ] Selectors use `data-test-id` via helper functions in the outermost `describe`, not classes or `id`.
- [ ] Each `it()` tests one behavior.
- [ ] Edge cases and error paths are covered.
- [ ] HTTP tests verify call args and post-response DOM state.

## BDD examples

See `rules/frontend-testing-examples.md`.
