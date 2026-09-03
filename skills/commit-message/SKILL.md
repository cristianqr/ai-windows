---
name: commit-message
description: Draft commit messages by matching the repo's existing git log format. Use when creating commits or writing commit messages in any client project.
---

# Commit Message Format

**Always match the repo's existing style** — run `git log -5 --format='%s'` first. Do not impose a format the project does not use.

Also check, when present:

- `CONTRIBUTING.md`, `COMMIT_CONVENTION.md`
- Project rules in the workspace (e.g. `rules/*.md`)
- `skills/commit-and-push/SKILL.md` for the full commit/push workflow

## Branch naming

- Branch names are the **ticket code** (e.g. `PROJ-1234`, `MANA-3003`).
- Do not prefix with `feature/`, `bugfix/`, or other paths unless the repo already uses that convention.

## Workflow

1. Run `git diff`, `git log -5 --format='%s'`, and read the current branch name.
2. Identify the dominant pattern in recent commits (ticket prefix, Conventional Commits, plain subject, etc.).
3. Resolve **ticket ID** from the branch name first, then the user request, PR, or recent commits. If required and missing, **ask the user** — do not guess or use placeholders.
4. Draft a specific subject that summarizes **why**, not just file names. Always add a body summarizing **what changed** (see "Commit body = PR body" below) — skip it only for a genuinely trivial, single-line change.
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

Body (after a blank line) summarizes **what changed**, plus **why** when it's not obvious from the
subject — see "Commit body = PR body" below.

## Common pattern — Conventional Commits

Use **only when** recent commits use `type(scope): subject` (e.g. `feat:`, `fix:`, `chore:`). Match the types and scopes already used in the log.

## Commit body = PR body

The commit body is not just a rationale note — it is reused **verbatim as the PR description**
when a PR is opened for this branch (see `skills/commit-and-push/SKILL.md` and any PR-creation
step). Write it so it stands alone for a reviewer with no other context:

- Summarize **what changed**, in prose or a short bullet list, at the level a reviewer needs.
- Add **why** only when it isn't obvious from the subject or the diff itself.
- Avoid commit-internal shorthand ("see above", "as discussed") — the reader is a PR viewer, not
  someone following the commit history.
- Multiple commits on one branch: keep each body scoped to that commit's own change. If the PR
  spans several commits, the PR-creation step composes the final PR body from all of them — don't
  try to write the whole PR narrative into a single commit.

## Mandatory when context exists

If the agent has context from `git diff`, the session, or an open PR, writing a proper message is **mandatory**:

1. Analyze what changed and **why** (intent).
2. Use a specific, accurate subject — never `new changes`, `updates`, `fix`, or `wip`.
3. Write a body summarizing the change (see "Commit body = PR body" above) — do not leave it empty
   unless the change is genuinely trivial and single-line.
4. Do **not** ask the user to write the message when the agent can draft it. Only ask for a missing required ticket ID.

## Examples (ticket-prefix repos)

```text
[PROJ-4521]: Fix null guard on order history filter

Guard against a null `order.history` when the API returns a shipment with no
tracking events yet, which crashed the timeline component on first render.
```

```text
[PROJ-4400]: Add order history V2 wrapper

Reuse existing wrapper pattern so V2 can toggle via config flag.
- Adds `OrderHistoryV2Wrapper` alongside the existing wrapper.
- Reads `FEATURE_ORDER_HISTORY_V2` from config to pick which one renders.
```

## Do not

- Impose a format that does not appear in `git log`.
- Use placeholder ticket IDs (`TICKET-ID`, `PROJ-XXXX`, `XXX-000`).
- Use vague subjects when context exists.
- Leave the body empty for a non-trivial change — it becomes the PR description.
- Commit secrets, `.env`, or credential files.
- Commit unless the user explicitly asked.
