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
├── frontend-general.mdc        ← alwaysApply: code style, commits
├── frontend-testing.mdc        ← globs: **/*-test.js (AAA, BDD, selectors, mocking)
├── frontend-testing-examples.md ← BDD and HTTP assertion examples
└── tailwind.mdc                ← globs: **/*.{vue,html,jsx,tsx}
```

Rule files use `.mdc` with YAML frontmatter:

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
| `frontend-general.mdc` | `true` | Line wrapping, braces, early returns, semantic naming, JSDoc, commit format `[TICKET-ID]: …` |
| `frontend-testing.mdc` | `**/*-test.js` | Behavior over implementation, `data-test-id`, AAA, BDD, mocking, `shallowMount` |
| `frontend-testing-examples.md` | — | Selector helpers, BDD `describe`/`it` naming, HTTP `toHaveBeenCalledWith` patterns |
| `tailwind.mdc` | `**/*.{vue,html,jsx,tsx}` | `design-platform`, Tailwind utilities, `rem` sizing, `data-style` when ESLint requires it |

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
