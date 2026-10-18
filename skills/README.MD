# Skills

Skills define **workflows and procedures** the AI agent follows when performing a specific task. They answer: *"How do I complete this job correctly?"*

## Skill lineup

| Skill | Trigger | Related rules |
|---|---|---|
| `implement-changes/` | Coding a ticket, bug fix, or feature | `frontend-general`, `vue-feature-architecture`, `tailwind`, `frontend-accessibility` |
| `write-unit-tests/` | Writing or updating `*-test.js` | `frontend-testing`, `frontend-testing-examples` |
| `review-changes/` | PR or diff review (frontend) | All relevant `rules/` |
| `review-changes-dotnet/` | PR or diff review (.NET/C# backend) | `dotnet-general`, `dotnet-testing` |
| `publish-pr-review/` | After review discussion, inline comments on the PR | — |
| `commit-changes/` | Commit, push, or draft commit message | `commit-messages` |
| `address-pr-feedback/` | Review comments on your own PR: discuss, fix, test, amend, push (asks first), update PR/plan, reply and resolve | `commit-messages` |
| `draft-release-announcement/` | Draft a release announcement from a version diff | `commit-messages` |
| `update-release-document/` | Fill the release-document questionnaire for the user's own PRs | — |

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
├── review-changes-dotnet/
│   └── SKILL.md
├── publish-pr-review/
│   └── SKILL.md
├── commit-changes/
│   └── SKILL.md
├── draft-release-announcement/
│   └── SKILL.md
└── update-release-document/
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
mkdir -p ~/.claude/skills/{commit-changes,implement-changes,write-unit-tests,review-changes,review-changes-dotnet,publish-pr-review,draft-release-announcement,update-release-document}

ln -sfn "$PWD/skills/commit-changes/SKILL.md" ~/.claude/skills/commit-changes/SKILL.md
ln -sfn "$PWD/skills/implement-changes/SKILL.md" ~/.claude/skills/implement-changes/SKILL.md
ln -sfn "$PWD/skills/write-unit-tests/SKILL.md" ~/.claude/skills/write-unit-tests/SKILL.md
ln -sfn "$PWD/skills/review-changes/SKILL.md" ~/.claude/skills/review-changes/SKILL.md
ln -sfn "$PWD/skills/review-changes-dotnet/SKILL.md" ~/.claude/skills/review-changes-dotnet/SKILL.md
ln -sfn "$PWD/skills/publish-pr-review/SKILL.md" ~/.claude/skills/publish-pr-review/SKILL.md
ln -sfn "$PWD/skills/draft-release-announcement/SKILL.md" ~/.claude/skills/draft-release-announcement/SKILL.md
ln -sfn "$PWD/skills/update-release-document/SKILL.md" ~/.claude/skills/update-release-document/SKILL.md
```

Run from this repo root.
