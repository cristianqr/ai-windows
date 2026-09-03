---
name: commit-changes
description: Draft a commit message, stage, commit, and optionally push to origin. Use when the user asks to commit, push, ship, or write a commit message. Default is commit only — push only when explicitly requested.
---

# Commit Changes

Workflow for staging, committing, and optionally pushing. Push to **`origin`** unless the user names a different remote.

Apply commit MUSTs in `rules/commit-messages.md`.

## Triggers

- `commit` — draft, stage, commit; no push.
- `commit and push` / `push` — draft, stage, commit, push to `origin`.
- `write a commit message` — draft and show only.

## Workflow

1. **Inspect** — run in parallel:
   - `git status`
   - `git diff` (staged and unstaged)
   - `git log -5 --format='%s'`
2. **Draft** — follow `rules/commit-messages.md`. Match the repo's log format. **Show the drafted message before committing.**
3. **Stage** — `git add` only relevant files. Never stage `.env`, credentials, or secrets.
4. **Commit** — pass the drafted subject/body via HEREDOC:
   ```bash
   git commit -m "$(cat <<'EOF'
   <drafted message>
   EOF
   )"
   ```
5. **Push** (only when explicitly requested) — first push on a branch: `git push -u origin HEAD`. After upstream is set: `git push`.
6. **Verify** — `git log -1 --format=full`; `git status` after push.

## Branch rename (when requested)

1. `git branch -m <new-name>`
2. `git push -u origin <new-name>`
3. Delete old remote branch only when the user **explicitly** asks: `git push origin --delete <old-name>`

## Safety

- NEVER update git config.
- NEVER run destructive commands (`push --force`, `reset --hard`, remote branch delete) unless explicitly requested.
- NEVER skip hooks (`--no-verify`) unless explicitly requested.
- NEVER force-push to `main`/`master` — warn the user if they request it.
- NEVER commit or push unless the user explicitly asked.
- If a pre-commit hook fails, fix the issue and create a **new** commit — do not `--amend` unless amend rules are met.

## Amend rules

Use `git commit --amend` only when ALL are true:

1. User explicitly requested amend, OR hook auto-modified files after a successful commit.
2. HEAD commit was created in this session.
3. Commit has NOT been pushed to remote.

If amend is needed after push, use `--force-with-lease` only when the user explicitly asked.

If commit failed or was rejected by a hook, always create a new commit.
