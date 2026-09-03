# Skills

Skills define **workflows and procedures** the AI agent follows when performing a specific task. They answer: *"How do I complete this job correctly?"*

## What skills are

- **Task playbooks** — step-by-step checklists, templates, and decision trees.
- **On-demand** — loaded when the task matches the skill's description (e.g. writing tests, creating commits, reviewing PRs).
- **Procedural tone** — written as "when doing X, follow these steps…".

## What belongs here

| Put in **skills** | Examples |
|---|---|
| Multi-step workflows with decision points | PR review checklist, deploy procedure |
| Tasks that require reading context first | Analyze a git diff, then format a commit message |
| Long playbooks better loaded on demand | Test review process with examples |

## What does **not** belong here

- **Mandatory conventions** — even if task-specific, if it is a MUST it usually belongs in **rules**, scoped to the relevant files (see below).
- Always-on coding standards — use `rules/` instead.

## Gray area: rules vs skills

| Guideline | As a **rule** (scoped) | As a **skill** (on demand) |
|---|---|---|
| AAA / BDD in tests | `**/*-test.js` → "MUST follow AAA, never assert internal state" | "When writing tests, follow this methodology…" |
| Commit message format | "All commits MUST match `[TICKET-ID]: …`" | "When asked to write a commit, analyze the diff and apply this template…" |
| PR review structure | Rarely a rule — no file glob triggers it | "When reviewing a PR, follow this checklist…" |

**Use rules when** the constraint applies whenever certain **files** are edited — even if the content is long. Scope with globs instead of putting MUST conventions in skills.

**Use skills when** the work is **task-triggered** (review this PR, format this commit) and involves steps, context gathering, or branching logic — not just "never do X."

## Folder layout

Each skill is a directory with a `SKILL.md` file:

```
skills/
├── README.MD
├── commit-message/
│   └── SKILL.md
├── commit-and-push/
│   └── SKILL.md
├── frontend-testing/
│   └── SKILL.md
└── pr-review/
    └── SKILL.md
```

Symlink into Claude Code (from repo root):

```
.claude/skills/<skill>/SKILL.md  →  ../../skills/<skill>/SKILL.md
```

See root `README.MD` for symlink commands.

### SKILL.md structure

```markdown
---
name: skill-name
description: What this skill does and when to use it (include trigger terms)
---

# Skill Title

## Instructions
Step-by-step guidance for the agent.
```

The `description` field is critical — the agent uses it to decide when to apply the skill.

## Index / router pattern

Root config (`.claude/CLAUDE.md`) references skills by path; it does not duplicate skill content:

```markdown
For task-specific workflows (tests, commits, PR review), follow the matching
skill in `skills/`.
```

## Skills vs rules

| | **Rules** | **Skills** |
|---|---|---|
| Role | Constraints & standards | Workflows & procedures |
| Trigger | Automatic (always or by file) | When a specific task comes up |
| Analogy | Company policy | Runbook for a task |

See `rules/README.MD` for the rules definition.

## Practical split for this repo

| Skill | Purpose | Related rules |
|---|---|---|
| `commit-message/` | Draft messages; branch name = ticket code; format `[TICKET-ID]: …` | `rules/frontend-general.mdc` |
| `commit-and-push/` | Inspect, stage, commit, push to `origin` | `commit-message` |
| `frontend-testing/` | Write/review tests; discover Jest setup from the repo | `rules/frontend-testing.mdc`, `rules/frontend-testing-examples.md` |
| `pr-review/` | PR checklist — config, a11y, testing, component design | `rules/tailwind.mdc`, `rules/frontend-testing.mdc` |
