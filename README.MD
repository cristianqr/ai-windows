# ai-windows

Agent rules and skills for frontend work with **Claude Code**. Canonical content lives in two folders — root config files point here; they do not duplicate rule or skill text.

## Layout

```
ai-windows/
├── README.MD
├── rules/                          ← MUST conventions (.mdc + examples)
│   ├── frontend-general.mdc        ← alwaysApply: code style, commits
│   ├── frontend-testing.mdc        ← globs: **/*-test.js
│   ├── frontend-testing-examples.md
│   └── tailwind.mdc                ← globs: **/*.{vue,html,jsx,tsx}
└── skills/                         ← task workflows (on demand)
    ├── commit-message/
    ├── commit-and-push/
    ├── frontend-testing/
    └── pr-review/
```

## Rules index

| File | Scope | Summary |
|---|---|---|
| `frontend-general.mdc` | Always | Code style, JSDoc, commit format `[TICKET-ID]: …` |
| `frontend-testing.mdc` | `**/*-test.js` | AAA, BDD, `data-test-id` selectors, mocking |
| `frontend-testing-examples.md` | Reference | BDD naming, selector helpers, HTTP assertions |
| `tailwind.mdc` | `**/*.{vue,html,jsx,tsx}` | Tailwind utilities, `design-platform`, `data-style`, `rem` sizing |

See `rules/README.MD` for rules vs skills and Claude setup.

## Skills index

| Skill | Trigger |
|---|---|
| `commit-message` | Drafting commit messages |
| `commit-and-push` | Stage, commit, push to `origin` |
| `frontend-testing` | Writing or reviewing `*-test.js` tests |
| `pr-review` | PR / code review checklist |

See `skills/README.MD` for skill structure and symlinks.

## Claude Code setup

### Router (`.claude/CLAUDE.md`)

Keep the project router thin — point at canonical folders:

```markdown
Before writing or modifying frontend code, you MUST read and strictly adhere
to all rules in `rules/`.

For task-specific workflows (tests, commits, PR review), follow the matching
skill in `skills/`.
```

### Skills symlinks

Run from this repo root:

```bash
mkdir -p .claude/skills/{commit-and-push,commit-message,frontend-testing,pr-review}

ln -sf "$PWD/skills/commit-and-push/SKILL.md" .claude/skills/commit-and-push/SKILL.md
ln -sf "$PWD/skills/commit-message/SKILL.md" .claude/skills/commit-message/SKILL.md
ln -sf "$PWD/skills/frontend-testing/SKILL.md" .claude/skills/frontend-testing/SKILL.md
ln -sf "$PWD/skills/pr-review/SKILL.md" .claude/skills/pr-review/SKILL.md
```

Optional — global skills (same commands with `~/.claude/skills/` instead of `.claude/skills/`).

## Git conventions (this workspace)

- **Branch name:** ticket code (e.g. `PROJ-1234`)
- **Commit message:** `[TICKET-ID]: Short description`
- **Push remote:** `origin` (single remote)
