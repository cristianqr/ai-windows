---
name: update-release-document
description: Fill in the Confluence release-document questionnaire (Risk Rating, test coverage, dashboards, Prod sanity test, etc.) for the user's own PRs in a release, using the real PR/commit content and prior release docs as a tone reference. Use when the user asks to fill in, complete, or update a release document/questionnaire, or to answer the "[To be filled]" fields on a ToolBob-style release page. Never fills or removes another author's PR section without asking first.
---

# Update Release Document

Fill the `[To be filled]` questionnaire cells on a Confluence release-document page — but only for PRs the user actually authored. Leave every other author's section untouched.

## Required inputs — ask if missing

- **Release document link** — the Confluence page URL or ID to update.
- **Whose changes** — default to the current user (match by Jira/GitHub author against the account behind `userEmail`/git config), but confirm if the page's author labels don't obviously map to that identity (e.g. `c-cquispe_devopssb` vs `c.cquispe@shipbob.com` — ask if the mapping isn't obvious).

## Workflow

1. **Resolve the page** — get `cloudId` (try the site hostname first, e.g. `shipbob.atlassian.net`), then `getConfluencePage` with `contentFormat: "html"`. Always use HTML, not markdown — this repo's release docs contain raw HTML (embedded `<img>` tags, smart-link macros) that markdown can't round-trip, and `updateConfluencePage` will reject a markdown full-body replace with a 422 data-loss error. Before writing back, call `getContentFormatGuide` with `toolName: "updateConfluencePage"` and follow it.

2. **Find the user's own PR sections** — each `####` heading in the Questionnaire is `PR #N: <title> (<author-handle>)`, and the Changes table has an `@Mention` per row. Match these against the user's identity. Only sections that match are in scope for the rest of this workflow — do not touch any other section's cells, including ones with a shared ticket key.

3. **Classify each of the user's own PRs**:
   - **Real change** (has a Jira ticket, or is a genuine code change) — fill it (step 4).
   - **Merge/sync no-op** (titles like `Dev > Master`, `develop -> master`, `sync master into develop`, or a PR body that's empty/just a merge with no functional change) — do **not** fill it with invented content. Flag it to the user by name/PR number and ask whether to remove its row from the Changes table and its whole Questionnaire section, or leave it as `[To be filled]`. Only remove after the user confirms — never remove unilaterally.

4. **Fill a real-change section** — for each `[To be filled]` cell (typically Risk Rating, "What is most likely to go wrong...", "Were new unit/integration tests added...", "Business KPI dashboards and alerts", "Sanity/impact test suite for Prod"):
   - Pull the actual PR body (`gh api repos/{owner}/{repo}/pulls/{n}`) for real test/risk/deployment content — never invent facts not in the PR.
   - Match the tone/format/level of detail to a **prior completed release doc** in the same Confluence space, not to the placeholder examples on this page (most recent release docs are often still all `[To be filled]` too — search back further with `searchConfluenceUsingCql` on `title ~ "<App> Release"` until you find one with real answers, e.g. `Risk Rating: Low — UI-only change...`).
   - Leave already-answered cells (Operational urgency, Engineering metrics dashboards, Rollback plan, the No/No/No/No block) alone — only touch `[To be filled]`.

5. **Write back** — reconstruct the full page body from the HTML fetched in step 1, changing only the target `<p data-local-id="...">` text content (and removing a row/section only if step 3 got explicit confirmation). Preserve every `data-local-id` and every other author's content byte-for-byte. Pass `contentFormat: "html"` and a `versionMessage` describing what was filled.

6. **Report** — which PR sections were filled, which were left untouched (not the user's own), and which merge/sync PRs were flagged/removed per the user's answer.

## Do not

- Do not fill or remove another author's PR section.
- Do not invent Risk Rating, test coverage, or dashboard content not backed by the actual PR body.
- Do not remove a merge/sync PR's row/section without asking first, even when it's clearly a no-op.
- Do not do a markdown full-body replace on a page with embedded HTML/macros — use HTML and follow `getContentFormatGuide`.
- Do not touch a `data-local-id` on content you aren't changing.
