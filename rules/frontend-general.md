---
description: General frontend conventions for MFE repos
alwaysApply: true
---

# General Frontend Practices

Applies to **Vue MFE** client projects. Match existing patterns in the target repo before introducing new ones.

## Rules index

| When editing | Apply |
|---|---|
| Any frontend code | This file |
| `*.vue` feature modules | `rules/vue-feature-architecture.md` |
| Markup styling | `rules/tailwind.md` |
| Markup accessibility | `rules/frontend-accessibility.md` |
| `*-test.js` | `rules/frontend-testing.md` |
| Commit messages | `rules/commit-messages.md` |

**Workflows** (procedures, not MUSTs): `skills/implement-changes`, `skills/write-unit-tests`, `skills/review-changes`, `skills/commit-changes`.

## Code style

1. **Maximum line length — 120 characters**, enforced by ESLint and pre-commit hooks. Break long template attributes, object literals, and chained calls across multiple lines to stay under it. Let Prettier handle formatting; do not hand-reformat unrelated code.
2. **Braces are mandatory** — every `if`/`else`/`for`/`while` body MUST use `{}`, even single statements.
3. **Preserve existing formatting** — never reformat or re-indent code you are not otherwise changing. A diff MUST show only lines relevant to the task.
4. **Reuse before you build** — search for an existing component, composable, service, or logic module before writing a new one. Prefer extending an existing pattern over introducing a parallel one.
5. **Semantic naming over structural naming** — name variables/functions after what the data or operation *means* in the domain, not the data structure it sits in. Prefer `CARRIER_SHIPPING_RATES` over generic `ITEMS`/`DATA`/`ROWS`. When an object's property name is fixed by an external contract (e.g. a UI library's `children` field), keep the required property name on the object but name the local variable for what it contains: `const cptSlotNodes = ...; return { ..., children: cptSlotNodes };`, not `const children = ...; return { ..., children };`.
6. **Early return over nested conditionals** — in new code, guard-clause / early-return out of a function rather than wrapping the remaining body in an `if`/`else`.
   - ❌ `if (isValid) { doThing(); if (isReady) { doOther(); } }`
   - ✅ `if (!isValid) return; doThing(); if (!isReady) return; doOther();`
7. **No speculative abstraction** — don't introduce a shared helper, prop, or config option for a single call site "in case it's needed later." Three similar lines beat a premature abstraction.
8. **No backwards-compatibility shims** — don't rename-and-reexport, leave `// removed` comments, or keep unused branches "just in case." If it's unused, delete it.
9. **Comments explain WHY, not WHAT** — skip comments that restate what the code already says. Write comments only for non-obvious constraints, invariants, or workarounds. Comments MUST be in English.
10. **Don't reference tickets/tasks in code** — no `// PIK-1234` or `// fix for X flow` comments in source. That context belongs in the PR description, not in code that outlives the ticket.
11. **No TypeScript — use JSDoc** — repos are JavaScript-only unless the target repo already uses TypeScript. Document params, returns, and non-obvious shapes with JSDoc (`@param`, `@returns`, `@emits`). Do NOT introduce `.ts` files unless the repo already uses them.

## Timezone & data contracts

- **Timezone-aware date bucketing** — when bucketing or comparing by calendar date, always convert through the relevant local timezone first. Never compare raw UTC date substrings directly.
- **UTC is the wire format** — the frontend/backend contract is UTC. Only reformat the value for display; never mutate the underlying datetime value.

## Commit & PR conventions

- **Commit format:** see `rules/commit-messages.md` and `skills/commit-changes/SKILL.md`.
- Version bumps are automated via Jenkins.
