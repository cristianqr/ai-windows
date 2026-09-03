---
name: implement-changes
description: Implement frontend features and fixes in Vue MFE apps. Use when coding a ticket, bug fix, or spec requirement. Conventions are in rules/.
---

# Implement Changes

Workflow for **implementing** frontend changes — not for test-only work, review, or commit.

Apply all MUST conventions in `rules/frontend-general.md`. Also apply:

| Concern | Rule |
|---|---|
| Feature module structure | `rules/vue-feature-architecture.md` |
| Styling | `rules/tailwind.md` |
| Accessibility | `rules/frontend-accessibility.md` |

## Inputs

- Required: clear requirement — from a ticket description, spec section, or user instruction.
- Optional: target feature module, config keys, acceptance criteria.

If the requirement is ambiguous, **stop and ask once** — do not guess.

## Pre-implementation exploration (required)

Before writing any code:

1. Read the target component, container, service, or logic module in full.
2. Read the existing test file — mount wrappers, shared mocks, local patterns.
3. Verify config key paths and casing against actual config files in the target repo.
4. Search for existing components, composables, services, or logic modules to reuse.

## Implementation rules

- Keep scope limited to the stated requirement. Do **not** refactor, rename, or reformat code outside that scope.
- Do **not** add features or optimizations not described in the requirements.
- Respect feature module boundaries — pages, containers, components, services, logic, store.
- Key-case conversion (`snake_case` ↔ `camelCase`) belongs in the **service layer only**.
- Pure business logic in `logic/` — no Vue/Vuex imports; pass `$t` as a parameter when i18n is needed.
- If a requirement is ambiguous or missing, **stop and ask** — do not invent behavior.

## Tests

When behavior changes, update or add tests. For authoring tests, follow `skills/write-unit-tests/SKILL.md` and `rules/frontend-testing.md`.

## Verification (required)

After completing implementation, run and confirm they pass:

```bash
npm run test <path/to/affected-test-file>
npm run lint
```

Use the lint command the repo defines (`npm run lint`, `npm run lint-ci`, etc.).

If either fails, fix and re-run before reporting done.

## Completion output

Report:

1. Files changed
2. Requirement-by-requirement implementation status (when multiple requirements)
3. Test/lint commands executed and results
4. Remaining risks or follow-ups

## Not in scope

- **Writing tests only** — use `skills/write-unit-tests/SKILL.md`.
- **Reviewing changes** — use `skills/review-changes/SKILL.md`.
- **Commit / push** — use `skills/commit-changes/SKILL.md` when the user asks to commit.
