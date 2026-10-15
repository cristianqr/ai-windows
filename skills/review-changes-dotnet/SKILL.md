---
name: review-changes-dotnet
description: Review .NET/C# backend code changes for style, async/EF Core conventions, error handling, and test coverage. Use when reviewing pull requests or diffs in a .NET/C# repo.
---

# Review Changes (.NET)

Review workflow for .NET/C# backend PRs and local diffs — style, async/EF Core hygiene, error handling, test coverage, and PR hygiene.

Before reviewing, apply all relevant rules in `rules/` (especially `general.md`, `dotnet-general.md`, `dotnet-testing.md`) plus any project-specific conventions in that repo's own `CLAUDE.md` — project conventions win over these general ones when they conflict.

## Workflow

1. Read the diff and description — identify purpose, scope, and ticket.
2. Check **one PR = one purpose** — flag unrelated changes (formatting-only, dependency bumps, drive-by refactors).
3. Review against the checklist below and rule MUSTs.
4. Format feedback by severity:
   - 🔴 **Critical** — must fix before merge
   - 🟡 **Suggestion** — consider improving
   - 🟢 **Nice to have** — optional
5. To put the agreed findings on the PR as inline comments after discussion, use `skills/publish-pr-review`.

## Quick checklist

- [ ] `CancellationToken` threaded through every async call that accepts one — not dropped partway through a chain.
- [ ] Read-only EF Core queries use `.AsNoTracking()`.
- [ ] No hand-edited `.Designer.cs` / `*ModelSnapshot.cs` migration files.
- [ ] New/changed queries don't introduce client-side evaluation without a deliberate reason.
- [ ] Error responses match the project's existing shape (`ProblemDetails`/custom envelope) — no ad hoc alternative introduced.
- [ ] Exception messages include the relevant entity ID(s), not just a generic description.
- [ ] No business-rule correctness fix gated behind a feature flag unless the flag is for a genuine experiment or per-tenant/environment variation.
- [ ] No unrelated changes bundled (formatting-only diffs, dependency bumps, unrelated refactors).
- [ ] New public members have XML doc comments describing the contract, not restating the name.
- [ ] Comments explain *why*, not *what* — none restating what well-named code already shows.
- [ ] New local variables/parameters precisely name the domain concept they hold, especially when reading a specific entity property — flag a vague paraphrase (e.g. `currentQuantity`) that drops the distinguishing part of the source name (e.g. `CompletedPickQuantity`). See `rules/general.md`.
- [ ] Test method names follow the project's existing convention (check neighboring files, don't assume).
- [ ] New tests use the project's existing mocking/fixture patterns — no new pattern introduced without reason.
- [ ] Tests added for new failure-path branches (validation, not-found, conflict), not just the happy path.
- [ ] For a business-rule change: confirm existing tests were audited, not just left passing on stale seed data (a still-green test can be asserting the *old* rule).

## Error handling

- New failure paths get a specific, named exception or the project's established error type — not a bare `throw new Exception(...)`.
- If the project uses a structured error-code pattern (e.g. a per-area problem catalog), new endpoints use it rather than constructing an ad hoc error inline.

## EF Core & data access

- Migrations are additive and reversible where the project's convention expects a `Down()`; note when one is deliberately irreversible and why.
- New indexes/constraints: confirm the PR states why (query pattern it serves, or invariant it enforces) — an unexplained index or constraint is a review flag, not a nitpick.
- Watch for N+1 query patterns introduced by looped single-row queries instead of a batched one.

## Testing

- Prefer a real dependency (Testcontainers-backed DB, etc.) over mocking the ORM for anything that exercises actual query/persistence behavior.
- One behavior per test; don't bundle multiple scenarios into one test method with several unrelated assertions.
- Flag tests that assert on private/internal state via reflection instead of observable behavior.

## PR discipline

- Match the naming/style of sibling files in the same folder.
- Follow the project's existing namespace/using-directive ordering.
- Migration file names follow the project's timestamp+description convention, not a generic name.
