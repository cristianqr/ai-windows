---
name: commit-and-push
description: Stage, commit, and push changes to remote. Use when the user asks to commit, push, or ship current changes. Works across client projects — discover each repo's commit format from context.
---

# Commit and Push

Generic workflow for any git repo. **Do not assume** a specific commit format — discover it per project. Push to **`origin`** unless the user names a different remote.

## Discover project conventions (before commit)

1. Run `git log -5 --format='%s'` and **match the existing commit message style** (prefix, ticket ID, scopes, etc.).
2. Check for project docs: `CONTRIBUTING.md`, `COMMIT_CONVENTION.md`, `.github/pull_request_template.md`.
3. If the workspace includes `skills/commit-message/SKILL.md`, use it **when the repo's log format matches**.
4. Resolve **ticket/issue ID** from the user request, branch name, or recent commits. If required by the project and missing, **ask the user** — do not guess.

## Workflow

1. **Inspect** — run in parallel:
   - `git status`
   - `git diff` (staged and unstaged)
   - `git log -5 --format='%s'`
2. **Draft** — mandatory when context exists. Write a complete message in the **repo's format**. **Show the drafted message before committing.** If a required ticket ID is missing, ask for it only — do not ask the user to write the full message.
3. **Stage** — `git add` only relevant files. Never stage `.env`, credentials, or secrets.
4. **Commit** — pass the subject and optional body via HEREDOC:
   ```bash
   git commit -m "$(cat <<'EOF'
   <subject in repo format>

   Optional body explaining why.
   EOF
   )"
   ```
5. **Push** — first push on a branch: `git push -u origin HEAD`. After upstream is set: `git push`.
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
