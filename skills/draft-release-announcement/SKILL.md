---
name: draft-release-announcement
description: Draft a release announcement from the commit/PR diff between a current version and the version being released. Use when the user asks to write, draft, or generate a release announcement, release notes, or "what's in this release" summary, given (or after asking for) a base version and a target version. Categorizes changes into Bug Fixes, Enhancements/Features, and Configuration Updates.
---

# Release Announcement

Turn the commit history between two versions into a release announcement matching the team's standard format (header, release docs list, categorized changes, downtime note).

## Required inputs — ask if missing

Never guess these. Ask the user directly:

- **Base version** — the version currently live (tag, ref, or commit).
- **Target version** — the version being released (tag, ref, or branch).
- **Repo** — default to the current repo if inside one; otherwise ask for `owner/repo`. Derive the app name for the header from the repo (repo name, or `gh repo view` description) — don't ask for it separately.
- **Release date and time**, including timezone(s) to display (the template shows local + UTC, e.g. `4:10 PM CST (10:10 PM UTC)`).
- **Downtime note** — do not default to "no expected downtime"; ask what the actual deployment impact is.

## Workflow

1. **Fetch history** — `git fetch --tags --quiet`, then get the commit list between the two refs:
   ```bash
   git log <base>..<target> --format='%s'
   ```
   If either ref isn't available locally, use `gh api repos/{owner}/{repo}/compare/{base}...{target}` instead. Prefer the `gh api compare` form even when git is available — it also returns `status`/`ahead_by`/`behind_by`, needed for the next step.

2. **Exclude backmerges** — check the compare's `status` field. If `status` is `"diverged"` (`behind_by > 0`), the base ref isn't a clean ancestor of the target: some fix already shipped on the base's line via a different branch, and later got merged *into* the target line just to catch it up — it is not new for this release.
   - Run the reverse compare, `gh api repos/{owner}/{repo}/compare/{target}...{base}`, to list the commits unique to base.
   - For each commit/PR on the target side, check whether its content (commit message, or the PR it points at via "Backmerge from #NNN" in the body) matches one of the base-only commits. A match means it's a backmerge of an already-released fix — drop it entirely from the announcement, ticket key and all, even though it's technically in the range.
   - When unsure whether a match is a genuine backmerge vs. coincidentally similar wording, ask the user rather than guessing.

3. **Extract tickets** — this repo's commit format is `[TICKET-ID]: Short description` (see `rules/commit-messages.md`). Parse each subject with `^\[([A-Z]+-\d+)\]:\s*(.+)$`.
   - Commits matching this pattern carry a ticket key and description.
   - Commits without a ticket key (dependency bumps, chores, config) go in a holding list — they usually belong under Configuration Updates.

4. **Dedupe by ticket** — multiple commits can share a ticket key. Merge them into one entry. If the descriptions describe genuinely distinct changes, list them as sub-bullets under that ticket (see the `RIM-1913` example in the template below); if they're the same change refined over commits, keep only the clearest single line.

5. **Categorize** — for each ticket:
   - If Jira MCP tools are available, look up the issue type (`getJiraIssue` / `mcp__atlassian__getJiraIssue`): `Bug` → Bug Fixes, `Story`/`Task`/`Improvement`/`Epic` → Enhancements/Features.
   - If Jira lookup isn't available or fails, ask the user to confirm category for ambiguous tickets rather than guessing from keywords alone.
   - Non-ticket items (dependency/security bumps, infra/config changes) → Configuration Updates.
   - Drop a category section entirely if it has no items — do not emit an empty header.

6. **Release Document is a plain placeholder line** — just the text "Release Document", no link and no version number. The user adds the actual link themselves when posting.

7. **Draft** — fill the template below exactly. Use the real commit/PR description text; never invent or embellish a change. Present the draft to the user for confirmation before treating it as final — this skill does not post to Slack, email, or anywhere else on its own.

## Output template

```text
{App Name} {base version} → {target version} Release Announcement
📅 {Weekday, Month DD, YYYY}

⏰ {H:MM AM/PM TZ1} ({H:MM AM/PM TZ2})


Release Document

In this Release

🐛 Bug Fixes
{TICKET-ID}: {description}
...

🏁 Enhancements/Features
{TICKET-ID}: {description}
{TICKET-ID}:
{sub-bullet description}
{sub-bullet description}
...

⚙️ Configuration Updates
{item 1, e.g. a flag/config removal}

{item 2, e.g. dependency/security bumps}

{downtime note}
```

Omit the 🐛, 🏁, or ⚙️ section entirely if it has no items for this release. Blank line between each item in Configuration Updates (including before the downtime note) — that section reads as separate paragraphs, unlike Bug Fixes/Enhancements which stay one line per item with no blank line between.

## Do not

- Do not assume "no expected downtime" — ask.
- Do not fabricate ticket descriptions — use the actual commit/PR/Jira summary text.
- Do not guess bug-vs-feature categorization for a ticket when it's ambiguous and Jira lookup isn't available — ask the user.
- Do not include a backmerge of an already-released fix just because its commit falls inside the base..target range — check divergence first.
- Do not list version numbers or a link under Release Document — plain placeholder text only; the user adds the link when posting.
- Do not publish or send the announcement anywhere unless the user explicitly asks.
