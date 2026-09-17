---
name: publish-pr-review
description: Publish an agreed PR review as inline comments on the matching diff lines after the review conversation. Use when the user asks to add comments to the PR, comment on the corresponding lines, publish the review, post inline review comments, or to put observations on the PR after discussing a code review. Works in any GitHub or GitHub Enterprise repo. Do not use for the review itself or for a general issue comment unless the user asks for one.
---

# Publish PR Review

Turn the **agreed** review (chat findings plus what the user confirmed, dropped, or decided) into **inline GitHub review comments** on the corresponding diff lines.

This skill does **not** replace the review. Review and discuss first. Publish only when the user asks to put comments on the PR.

## When to run

- The user has already reviewed or discussed a PR in this conversation.
- They ask to add comments, publish the review, or comment on the corresponding lines.

If there is no review yet, review in chat first (use `skills/review-changes` when that skill exists in the current repo), wait for discussion, then come back here.

## Default behavior

- **Inline only** — `POST .../pulls/{n}/reviews` with `event: COMMENT` and one comment per finding on a `RIGHT` diff line. Do not post a general issue comment unless the user asks for one.
- **Agreed findings only** — include what the conversation settled. Drop items the user said need no change, are not defects, or were praise/clarification. Apply user corrections.
- **Short prose** — each comment is a few sentences. No markdown dash/bullet lists (`-`). Use commas and connected sentences. No severity emoji on the PR.
- **Map to code** — every comment sits on a changed line that owns that finding. Do not dump the whole review on the PR description.

## Optional inputs

- `pr=<url|number>` — PR to comment on. If omitted, use the PR already in the conversation.

## Workflow

1. **Collect** — from this conversation, list only findings that still stand after discussion. For each: the observation, any agreed fix, and the file/hunk it belongs to.

2. **Resolve the PR** — parse `owner`, `repo`, and pull number from the PR URL. Derive the GitHub host from that URL (`github.com` or the enterprise hostname). Call `gh api repos/{owner}/{repo}/pulls/{n}` for `head.sha`. Set `GH_HOST` to the URL host when it is not `github.com`. Confirm the latest head; comments must target that commit.

3. **Map lines** — for each finding, pick a **changed** line on the PR branch (`git show {head}:path` + the pull diff). GitHub only accepts comments on lines in the latest diff (`path`, `side: RIGHT`, `line`). Prefer the line that implements the issue, not a nearby unchanged line.

4. **Draft** — one short paragraph per finding. Put the agreed resolution in the comment when the chat chose one. Skip claims the user corrected. Skip callouts they said are already correct.

5. **Publish** — one review containing all inline comments:

   ```bash
   gh api -X POST repos/{owner}/{repo}/pulls/{n}/reviews --input payload.json
   ```

   Set `GH_HOST` from the PR URL when the host is not `github.com`.

   Payload shape:

   ```json
   {
     "commit_id": "<head sha>",
     "event": "COMMENT",
     "body": "",
     "comments": [
       {
         "path": "src/example.js",
         "side": "RIGHT",
         "line": 108,
         "body": "Short prose on this line, commas not dashes."
       }
     ]
   }
   ```

   If the API rejects a line (not in the diff), drop or retarget that one comment and retry. Do not fall back to a general issue comment.

6. **Report** — review URL and how many inline comments landed. Do not delete an existing general comment unless the user asks.

## Comment style

- Speak to the author: what is wrong or missing, and what to do.
- Name the agreed approach when the chat picked one.
- Keep each comment scoped to that line. Missing tests go on the test that gives false confidence, not on the implementation.

## Do not

- Re-review from scratch and ignore the conversation.
- Post the full review as a single issue comment when the user asked for line comments.
- Include dropped findings, praise-only notes, or unverified CI root causes.
- Use `-` bullet lists on the PR.
- Request changes (`event: REQUEST_CHANGES`) unless the user asks to block merge.
- Force-delete prior comments.
