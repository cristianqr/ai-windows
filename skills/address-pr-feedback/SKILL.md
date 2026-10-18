---
name: address-pr-feedback
description: Handle review comments on the user's own PR end to end: read unresolved threads, assess and discuss each fix with the user, apply it, run tests, amend or commit, push (after asking), update the PR description, commit message and Confluence dev plan, then reply to each thread and resolve it. Use when the user says reviewers or Copilot added comments, or asks to address, fix, or resolve PR comments.
---

# Address PR Feedback

Workflow for the author of a PR who has received review comments (humans or Copilot). Discuss before fixing, verify before pushing, and leave every thread answered and resolved.

Apply commit MUSTs in `rules/commit-messages.md` (no trailers or attribution, match the repo's log format). Use the `commit-changes` skill rules for staging and committing.

## Triggers

- "copilot added comments", "reviewer left comments", "address the PR comments", "resolve copilot comments".

## Ground rules

- **Discuss first.** Never change code for a comment before the user agrees with the approach.
- **Ask before pushing.** Always ask before `git push`, and especially before `--force-with-lease`. Approval for one push does not cover the next.
- **Never commit or push without the user's explicit go-ahead.** Showing the plan is not approval.
- **Do not change Jira ticket status.** Jira is not part of this workflow unless the user asks for a comment.
- Replies and PR text are **English, short and factual**. No AI attribution lines, no emojis, no verbatim quotes of colleagues.
- **Business-rule guardian.** If a requested change conflicts with a remembered project business rule, warn the user before applying it.
- Do not print or store tokens or secrets.

## Missing information (ask, do not guess)

Stop and ask the user when any of these cannot be found:

- the PR (resolve from the current branch with `gh pr view --json number,url`; ask if there is none or several);
- the ticket ID (branch name, PR title, recent commits; see `rules/commit-messages.md`);
- how to run the tests for the area touched (project `CLAUDE.md`, README, existing test projects);
- the Confluence dev plan page (search by title `[TICKET] Development Plan`; ask if there are several or none).

## Workflow

### 1. Read the feedback

1. Resolve owner/repo and PR number: `gh pr view --json number,url,headRefName,baseRefName`.
2. Fetch **unresolved** review threads with the GraphQL API (thread id, resolved/outdated flags, path, line, comments with `databaseId`, author, body):

   ```bash
   gh api graphql -f query='query{repository(owner:"OWNER",name:"REPO"){pullRequest(number:N){reviewThreads(first:50){nodes{id isResolved isOutdated path line comments(first:10){nodes{databaseId author{login} createdAt body}}}}}}}' --jq '.data.repository.pullRequest.reviewThreads.nodes[] | select(.isResolved==false)'
   ```

3. Also read general PR conversation comments and review summaries (`gh pr view N --comments`) in case feedback lives outside inline threads.
4. Check the local branch state: `git status -sb`, `git log --format='%h %s' origin/<base>..HEAD`, and whether HEAD is already pushed.

### 2. Assess each comment

For every unresolved thread, read the referenced code first, then decide:

- **Valid and actionable** — the defect or risk is real.
- **Valid but out of scope** — real, but belongs in a follow-up.
- **Not valid / already handled / by design** — explain why with evidence from the code or data.
- **Question or discussion** — needs an answer, not a code change.

Verify claims against the code (and data when a claim is about behaviour) before agreeing or disagreeing; automated reviewers are sometimes wrong and sometimes right about something subtle.

Present to the user, per comment: what it says, your verdict, the evidence, and **the proposed fix with alternatives and trade-offs** when there is more than one reasonable choice. Use a short table when there are several comments. **Wait for the user's decision** (they may pick an option, change it, or skip it).

### 3. Fix

1. Apply only what the user approved, matching the surrounding code's style and comments.
2. Follow the project's test conventions: update or add tests for the changed behaviour (assert behaviour, not implementation); if the fix invalidates an existing test, say so and update it deliberately.
3. Keep whitespace clean (`git diff --check`).

### 4. Test

1. Run the narrowest relevant tests first, then the wider suites the change could affect (project `CLAUDE.md` / README says how; start required infrastructure such as containers or env vars if the tests need it).
2. Report results honestly: counts passed/failed/skipped. If something fails, fix it or tell the user; do not hide it.
3. State clearly what was **not** run or verified.

### 5. Commit

Decide with the user how to record the change:

- **Single-commit PR and the user wants it kept single** — amend the existing commit (`git commit --amend`), only if HEAD was created in this session or the user explicitly asks. Amending a commit already on the remote needs a force push (see step 6).
- **Otherwise** — create a new commit `[TICKET]: description` matching the repo's log format.

When amending, **re-draft the commit message** if the change alters what the commit does (new design, renamed objects, changed behaviour). The body is reused as the PR description (`rules/commit-messages.md`). Show the drafted message before committing. Stage only relevant files; never `.env`, credentials or secrets; leave unrelated untracked files alone.

### 6. Push (ask first)

1. Show what will be pushed (`git log --oneline origin/<branch>..HEAD`, and whether history was rewritten).
2. **Ask the user to confirm** the push. If the commit was amended after being pushed, say that this needs `git push --force-with-lease origin <branch>` and that it rewrites the PR's history, and ask explicitly.
3. Never force-push `main`/`master`/`develop`. Never use plain `--force`.
4. After pushing, check `git status -sb` shows the branch in sync.

### 7. Update the PR description

1. Read the current body: `gh pr view N --json body --jq .body > <scratch>/pr.md`.
2. Update **only the parts that are now wrong or incomplete** (changes list, test counts and names, risks, "not verified" items). Keep the existing structure and the Jira/plan links.
3. Apply with `gh pr edit N --body-file <scratch>/pr.md`. Do not add attribution lines.
4. Skip this step only if nothing in the description changed, and say so.

### 8. Update the dev plan (Confluence)

1. Find the page `[TICKET] Development Plan` (search the planning folder; ask if unclear). Read it in markdown.
2. Update the sections affected by the fix (status, decisions, implementation steps, tests, risks, open questions) with a targeted edit, preserving the page structure. Add a version message describing the review change.
3. If there is no dev plan for the ticket, say so and skip.

### 9. Reply and resolve

For **every** thread that was addressed (including those declined or deferred):

1. Reply on the thread (English, 2-4 sentences): what changed or why not, and where. For declined/deferred items say the reason; for follow-ups mention the ticket if one exists.

   ```bash
   gh api -X POST repos/OWNER/REPO/pulls/N/comments/COMMENT_DATABASE_ID/replies -f body='...'
   ```

2. Resolve the thread only when the concern is dealt with (fixed, answered, or explicitly deferred/declined with the user's agreement):

   ```bash
   gh api graphql -f query='mutation{resolveReviewThread(input:{threadId:"THREAD_ID"}){thread{isResolved}}}'
   ```

3. Leave a thread **unresolved** if it needs the reviewer's answer, and tell the user.
4. Re-run the thread query to confirm no unresolved threads remain (or list the ones deliberately left).

### 10. Summary to the user

Short report: what each comment led to (fixed / answered / deferred), test results and what was not run, what was pushed (and how), what was updated (PR description, commit message, dev plan), which threads were resolved or left open, and any follow-up the user should decide on (for example a ticket for deferred work).

## Safety

- NEVER update git config.
- NEVER use `git push --force`; only `--force-with-lease`, only after the user confirmed, never on a protected base branch.
- NEVER skip hooks (`--no-verify`) unless explicitly requested.
- NEVER resolve a thread whose concern is not actually addressed.
- NEVER delete remote branches unless explicitly requested.
- If a hook fails, fix the cause and create a new commit instead of amending the failed one.

## Do not

- Start coding before the user picked an approach.
- Reply with generic text ("done", "fixed"); say what changed.
- Edit the PR description wholesale; make targeted edits.
- Skip the tests because the change looks small.
- Leave replies and the PR/plan out of sync with the code.
