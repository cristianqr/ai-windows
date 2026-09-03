# Skills

Skills define **workflows and procedures** the AI agent follows when performing a specific task. They answer: *"How do I complete this job correctly?"*

## Skill lineup

| Skill | Trigger | Related rules |
|---|---|---|
| `implement-changes/` | Coding a ticket, bug fix, or feature | `frontend-general`, `vue-feature-architecture`, `tailwind`, `frontend-accessibility` |
| `write-unit-tests/` | Writing or updating `*-test.js` | `frontend-testing`, `frontend-testing-examples` |
| `review-changes/` | PR or diff review | All relevant `rules/` |
| `commit-changes/` | Commit, push, or draft commit message | `commit-messages` |

## What skills are

- **Task playbooks** — step-by-step checklists and decision trees.
- **On-demand** — loaded when the task matches the skill's description.
- **Procedural tone** — written as "when doing X, follow these steps…".

## What belongs here vs rules

**Use rules when** the constraint applies whenever certain **files** are edited — scope with globs instead of putting MUST conventions in skills.

**Use skills when** the work is **task-triggered** (implement, review, commit) and involves steps, context gathering, or branching logic.

See the gray-area table in `rules/README.MD` and `skills/README.MD` (legacy section below) for examples.

## Folder layout

```
skills/
├── README.MD
├── implement-changes/
│   └── SKILL.md
├── write-unit-tests/
│   └── SKILL.md
├── review-changes/
│   └── SKILL.md
└── commit-changes/
    └── SKILL.md
```

This repo does **not** track `.claude/` — symlink skills globally (see below).

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

## Skills vs rules

| | **Rules** | **Skills** |
|---|---|---|
| Role | Constraints & standards | Workflows & procedures |
| Trigger | Automatic (always or by file) | When a specific task comes up |
| Analogy | Company policy | Runbook for a task |

See `rules/README.MD` for the rules definition.

## Gray area: rules vs skills

| Guideline | As a **rule** (scoped) | As a **skill** (on demand) |
|---|---|---|
| AAA / BDD in tests | `**/*-test.js` → MUST conventions | `write-unit-tests` workflow |
| Commit message format | `commit-messages.md` | `commit-changes` workflow |
| PR review structure | Rarely a rule | `review-changes` checklist |

## Index / router pattern

Client projects use a thin `.claude/CLAUDE.md` router that points at this repo's `rules/` and `skills/` — see root `README.MD`. Do not duplicate skill content in the router.

## Global Claude Code skills

```bash
mkdir -p ~/.claude/skills/{commit-changes,implement-changes,write-unit-tests,review-changes}

ln -sfn "$PWD/skills/commit-changes/SKILL.md" ~/.claude/skills/commit-changes/SKILL.md
ln -sfn "$PWD/skills/implement-changes/SKILL.md" ~/.claude/skills/implement-changes/SKILL.md
ln -sfn "$PWD/skills/write-unit-tests/SKILL.md" ~/.claude/skills/write-unit-tests/SKILL.md
ln -sfn "$PWD/skills/review-changes/SKILL.md" ~/.claude/skills/review-changes/SKILL.md
```

Run from this repo root.
