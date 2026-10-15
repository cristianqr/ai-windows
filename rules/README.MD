# Rules

Rules define **constraints and conventions** the AI agent must follow when writing or modifying code. They answer: *"What must always be true in this project?"*

For task workflows (implement, test, review, commit), see `skills/`.

## Rule catalog

| Rule | Trigger | Scope |
|---|---|---|
| [general.md](./general.md) | `alwaysApply: true` | Any language, any project — naming and other language-agnostic conventions |
| [frontend-general.md](./frontend-general.md) | `alwaysApply: true` | All frontend work — code style, rules index |
| [vue-feature-architecture.md](./vue-feature-architecture.md) | `**/*.vue` | Feature module boundaries, services, platform access |
| [tailwind.md](./tailwind.md) | `*.{vue,html,jsx,tsx}` | Tailwind utilities, CSS modules, sizing |
| [frontend-accessibility.md](./frontend-accessibility.md) | `*.{vue,html,jsx,tsx}` | `id`, ARIA, test selectors |
| [frontend-testing.md](./frontend-testing.md) | `**/*-test.js` | Unit test MUSTs (AAA, BDD, mocks, design-system stubs) |
| [frontend-testing-examples.md](./frontend-testing-examples.md) | Reference (not auto-loaded) | Selector helpers, HTTP assertion examples |
| [commit-messages.md](./commit-messages.md) | On demand | Commit message format, branch naming |
| [dotnet-general.md](./dotnet-general.md) | `alwaysApply: true` | .NET/C# backend work — code style, async, EF Core, error handling |
| [dotnet-testing.md](./dotnet-testing.md) | `**/*Tests.cs` | MSTest/NUnit/xUnit MUSTs (AAA, mocking, integration test hygiene) |

## Rules ↔ skills

| Rules (MUSTs) | Skill (workflow) |
|---|---|
| `general`, `frontend-general`, `vue-feature-architecture`, `tailwind`, `frontend-accessibility` | `implement-changes` |
| `frontend-testing` | `write-unit-tests` |
| All relevant rules | `review-changes` |
| — | `publish-pr-review` |
| `commit-messages` | `commit-changes` |
| `general`, `dotnet-general`, `dotnet-testing` | `review-changes-dotnet` |

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

- **Task workflows** that require gathering context or branching steps (use `skills/` instead).
- If a guideline is MUST whenever certain **files** are edited, it belongs here (scoped with globs), even if it looks like a "methodology." See the gray-area table in `skills/README.MD`.

## Folder layout

```
rules/
├── README.MD
├── general.md                 ← always on, language-agnostic
├── frontend-general.md        ← always on
├── vue-feature-architecture.md
├── tailwind.md
├── frontend-accessibility.md
├── frontend-testing.md
├── frontend-testing-examples.md
├── commit-messages.md
├── dotnet-general.md
└── dotnet-testing.md
```

Rule files use `.md` with YAML frontmatter:

```yaml
---
description: Brief description shown in the rule picker
globs: **/*-test.js
alwaysApply: false
---
```

## Claude Code setup

Point the agent at this repo's `rules/` from each client project's `.claude/CLAUDE.md` router — see root `README.MD`. Do not copy rule content into the router file.

## Rules vs skills

| | **Rules** | **Skills** |
|---|---|---|
| Role | Constraints & standards | Workflows & procedures |
| Trigger | Automatic (always or by file) | When a specific task comes up |
| Analogy | Company policy | Runbook for a task |

See `skills/README.MD` for the skills definition and index.
