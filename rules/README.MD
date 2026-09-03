# Rules

Rules define **constraints and conventions** the AI agent must follow when writing or modifying code. They answer: *"What must always be true in this project?"*

## What rules are

- **Standing standards** — naming, styling, file structure, testing, etc.
- **Automatic** — applied on every session, or when matching files are open (scoped via globs).
- **Mandatory tone** — written as MUST / never / always, not optional suggestions.

## What belongs here

| Put in **rules** | Examples |
|---|---|
| Conventions that apply whenever editing certain files | Use `data-test-id` for test selectors |
| Project-specific standards the agent would not infer | No TypeScript; JSDoc for public APIs |
| Guardrails and architectural constraints | Reuse existing components before creating new ones |
| MUST constraints scoped to file types | AAA/BDD in `**/*-test.js`, commit format |

## What does **not** belong here

- **Task workflows** that require gathering context or branching steps (use `skills/` instead) — e.g. "review this PR end-to-end", "analyze diff then write commit message."
- If a guideline is MUST whenever certain **files** are edited, it belongs here (scoped with globs), even if it looks like a "methodology." See the gray-area table in `skills/README.MD`.

## Folder layout

This folder is the **single source of truth** for rules:

```
rules/
├── README.MD
├── frontend-general.md        ← alwaysApply: code style, commits, timezone/data contracts
├── frontend-testing.md        ← globs: **/*-test.js (AAA, BDD, selectors, mocking)
├── frontend-testing-examples.md ← BDD and HTTP assertion examples
├── tailwind.md                ← globs: **/*.{vue,html,jsx,tsx}
└── vue-feature-architecture.md ← globs: **/*.vue (feature module boundaries, platform access)
```

Rule files use `.md` with YAML frontmatter:

```yaml
---
description: Brief description shown in the rule picker
globs: **/*-test.js
alwaysApply: false
---
```

## Rule summaries

| File | `alwaysApply` / globs | Contents |
|---|---|---|
| `frontend-general.md` | `true` | Line wrapping (120 chars), braces, early returns, semantic naming, no speculative abstraction/shims, no ticket comments, JSDoc, commit format `[TICKET-ID]: …`, timezone/UTC data contracts |
| `frontend-testing.md` | `**/*-test.js` | Behavior over implementation, `data-test-id`, AAA, BDD, mocking, `shallowMount`, design-system stub table |
| `frontend-testing-examples.md` | — | Selector helpers, BDD `describe`/`it` naming, HTTP `toHaveBeenCalledWith` patterns |
| `tailwind.md` | `**/*.{vue,html,jsx,tsx}` | `design-platform`, Tailwind utilities, `rem` sizing, `data-style` when ESLint requires it, CSS module naming |
| `vue-feature-architecture.md` | `**/*.vue` | `pages/containers/components/services/logic/store` split, service-layer casing, structural data, defensive platform access |

## Claude Code setup

Rules are loaded via the project router (`.claude/CLAUDE.md`) — see root `README.MD`. Point the agent at `rules/`; do not copy rule content into the router file.

Example router text:

```markdown
Before writing or modifying frontend code, you MUST read and strictly adhere
to all rules in `rules/`.
```

## Rules vs skills

| | **Rules** | **Skills** |
|---|---|---|
| Role | Constraints & standards | Workflows & procedures |
| Trigger | Automatic (always or by file) | When a specific task comes up |
| Analogy | Company policy | Runbook for a task |

See `skills/README.MD` for the skills definition and index.
