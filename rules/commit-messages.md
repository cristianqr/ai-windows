---
description: Commit message format and MUST conventions for all git commits
alwaysApply: false
---

# Commit Messages

Apply whenever drafting or writing a commit message. For the commit workflow, see `skills/commit-changes/SKILL.md`.

## Format (MUST)

**Always match the repo's existing style** — run `git log -5 --format='%s'` first. Do not impose a format the project does not use.

Common pattern for this workspace:

```text
[TICKET-ID]: Short description
```

- **TICKET-ID** — issue tracker ID (e.g. `PROJ-1234`, `MANA-3003`). **Ask the user** if missing.

Also check, when present: `CONTRIBUTING.md`, `COMMIT_CONVENTION.md`, project `rules/*.md`.

## TICKET-ID resolution

Look for the ID in, in order:

1. Current branch name (the ticket code)
2. User's request or conversation
3. Open PR title, description, or issue link
4. Recent commits on the branch

If required and missing, **stop and ask the user** — do not guess or use placeholders.

## Branch naming

- Branch names are the **ticket code** (e.g. `PROJ-1234`, `MANA-3003`).
- Do not prefix with `feature/`, `bugfix/`, or other paths unless the repo already uses that convention.

## Message quality (MUST when context exists)

When the agent has context (`git diff`, conversation, files edited, open PR):

1. Analyze what changed and **why** (intent), not just file names.
2. Produce a complete message before committing.
3. Use a specific, accurate subject — never `new changes`, `updates`, `fix`, or `wip`.
4. Write a body summarizing the change — see below. Skip body only for genuinely trivial single-line changes.
5. Do NOT ask the user to write the message when the agent already has enough context. Only ask for missing **TICKET-ID**.

## Commit body = PR body

The commit body is reused **verbatim as the PR description** when a PR is opened for this branch.

- Summarize **what changed** at the level a reviewer needs.
- Add **why** only when it isn't obvious from the subject or diff.
- Avoid commit-internal shorthand — the reader is a PR viewer.
- Multiple commits on one branch: keep each body scoped to that commit's change.

## Trailers (never)

**NEVER** append trailers or attribution — no `Co-authored-by:`, `Signed-off-by:`, or agent credits.

## Do not

- Impose a format that does not appear in `git log`.
- Use placeholder ticket IDs (`TICKET-ID`, `PROJ-XXXX`, `XXX-000`).
- Use vague subjects when context exists.
- Leave the body empty for a non-trivial change.
- Commit secrets, `.env`, or credential files.

## Examples

```text
[PROJ-4521]: Fix null guard on order history filter

Guard against a null order.history when the API returns a shipment with no
tracking events yet, which crashed the timeline component on first render.
```

## PR conventions

PRs MUST include: JIRA ticket link, description, test coverage, no new warnings, screenshots for UI changes, no console.logs, peer review.
