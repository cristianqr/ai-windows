# ai-windows

Agent rules and skills for frontend work with **Claude Code**. Canonical content lives in `rules/` and `skills/` — client projects wire them up via global `~/.claude` symlinks (this repo does **not** track `.claude/`).

## Layout

```
ai-windows/
├── README.MD
├── .gitignore                      ← excludes .claude/
├── rules/                          ← MUST conventions (.md + examples)
│   ├── frontend-general.md
│   ├── vue-feature-architecture.md
│   ├── tailwind.md
│   ├── frontend-accessibility.md
│   ├── frontend-testing.md
│   ├── frontend-testing-examples.md
│   └── commit-messages.md
└── skills/                         ← task workflows (on demand)
    ├── implement-changes/
    ├── write-unit-tests/
    ├── review-changes/
    └── commit-changes/
```

## Rules index

| File | Scope | Summary |
|---|---|---|
| `frontend-general.md` | Always | Code style, rules index, timezone/data contracts |
| `vue-feature-architecture.md` | `**/*.vue` | Feature module boundaries, service casing, platform access |
| `tailwind.md` | `**/*.{vue,html,jsx,tsx}` | Tailwind utilities, `rem` sizing, CSS module naming |
| `frontend-accessibility.md` | Markup files | `id`, ARIA, `data-test-id` |
| `frontend-testing.md` | `**/*-test.js` | AAA, BDD, selectors, mocking, design-system stubs |
| `frontend-testing-examples.md` | Reference | BDD naming, selector helpers, HTTP assertions |
| `commit-messages.md` | On demand | `[TICKET-ID]: …` format, branch naming, PR body |

See `rules/README.MD` for rules vs skills and the full catalog.

## Skills index

| Skill | Trigger |
|---|---|
| `implement-changes` | Coding features, fixes, or ticket work |
| `write-unit-tests` | Writing or updating `*-test.js` files |
| `review-changes` | PR / code review |
| `commit-changes` | Stage, commit, optional push to `origin` |

See `skills/README.MD` for skill structure and global setup.

## Claude Code setup (global)

Run from this repo root after clone or update:

```bash
mkdir -p ~/.claude/skills/{commit-changes,implement-changes,write-unit-tests,review-changes}

ln -sfn "$PWD/skills/commit-changes/SKILL.md" ~/.claude/skills/commit-changes/SKILL.md
ln -sfn "$PWD/skills/implement-changes/SKILL.md" ~/.claude/skills/implement-changes/SKILL.md
ln -sfn "$PWD/skills/write-unit-tests/SKILL.md" ~/.claude/skills/write-unit-tests/SKILL.md
ln -sfn "$PWD/skills/review-changes/SKILL.md" ~/.claude/skills/review-changes/SKILL.md
```

In each **client project**, add a thin `.claude/CLAUDE.md` router (local to that project — not tracked in this repo):

```markdown
Before writing or modifying frontend code, you MUST read and strictly adhere
to all rules in `<path-to-ai-windows>/rules/`.

For task-specific workflows, follow skills in `<path-to-ai-windows>/skills/`
(e.g. implement-changes, write-unit-tests, review-changes, commit-changes).
```

Replace `<path-to-ai-windows>` with the absolute path to this repo on your machine.

## Git conventions (this workspace)

- **Branch name:** ticket code (e.g. `PROJ-1234`)
- **Commit message:** `[TICKET-ID]: Short description`
- **Push remote:** `origin` (single remote)
