---
name: commit-message
description: Draft commit messages by matching the repo's existing git log format. Use when creating commits or writing commit messages in any client project.
---

# Commit Message Format

**Always match the repo's existing style** — run `git log -5 --format='%s'` first. Do not impose a format the project does not use.

Also check, when present:

- `CONTRIBUTING.md`, `COMMIT_CONVENTION.md`
- Project rules in the workspace (e.g. `rules/*.mdc`)
- `skills/commit-and-push/SKILL.md` for the full commit/push workflow

## Branch naming

- Branch names are the **ticket code** (e.g. `PROJ-1234`, `MANA-3003`).
- Do not prefix with `feature/`, `bugfix/`, or other paths unless the repo already uses that convention.

## Workflow

1. Run `git diff`, `git log -5 --format='%s'`, and read the current branch name.
2. Identify the dominant pattern in recent commits (ticket prefix, Conventional Commits, plain subject, etc.).
3. Resolve **ticket ID** from the branch name first, then the user request, PR, or recent commits. If required and missing, **ask the user** — do not guess or use placeholders.
4. Draft a specific subject that summarizes **why**, not just file names. Add a body when the why is not obvious from the subject.
5. **Show the drafted message before committing.**

## Common pattern — ticket prefix

Use **only when** recent commits match this shape:

```text
[TICKET-ID]: Short description
```

- **TICKET-ID** — issue tracker ID (e.g. `PROJ-1234`, `MANA-3003`). **Ask the user** if missing.

Look for the ID in, in order:

1. Current branch name (the ticket code)
2. User's request or conversation
3. Open PR title, description, or issue link
4. Recent commits on the branch

Optional body (after a blank line) explains **why**, not what.

## Common pattern — Conventional Commits

Use **only when** recent commits use `type(scope): subject` (e.g. `feat:`, `fix:`, `chore:`). Match the types and scopes already used in the log.

## Mandatory when context exists

If the agent has context from `git diff`, the session, or an open PR, writing a proper message is **mandatory**:

1. Analyze what changed and **why** (intent).
2. Use a specific, accurate subject — never `new changes`, `updates`, `fix`, or `wip`.
3. Do **not** ask the user to write the message when the agent can draft it. Only ask for a missing required ticket ID.

## Examples (ticket-prefix repos)

```text
[PROJ-4521]: Fix null guard on order history filter
```

```text
[PROJ-4400]: Add order history V2 wrapper

Reuse existing wrapper pattern so V2 can toggle via config flag.
```

## Do not

- Impose a format that does not appear in `git log`.
- Use placeholder ticket IDs (`TICKET-ID`, `PROJ-XXXX`, `XXX-000`).
- Use vague subjects when context exists.
- Commit secrets, `.env`, or credential files.
- Commit unless the user explicitly asked.
