---
description: C#/.NET code style, async, DI, and EF Core conventions for backend work
alwaysApply: true
---

# .NET General

Also applies `general.md` (language-agnostic naming, etc.) — this file only covers .NET-specific conventions.

## Code style

- Target the project's configured C# language version; prefer modern syntax (records, collection expressions `[]`, primary constructors, pattern matching) over older equivalents when the codebase already uses them.
- No underscore-prefixed private fields (`_foo`) unless the project's existing code already uses that convention consistently — match what's there, don't introduce a second style.
- Use `record` types for DTOs, requests, and responses; reserve `class` for entities and services with behavior.
- Constructor injection; prefer primary constructor syntax when the codebase has adopted it.

## Async & cancellation

- Every async method that can be given one accepts and threads through a `CancellationToken` — don't drop it partway through a call chain.
- Never block on async code (`.Result`, `.Wait()`, `GetAwaiter().GetResult()`) outside of a small number of known-safe entry points (`Main`, test setup).
- Use `Task`/`ValueTask` consistently with the surrounding codebase's convention; don't mix without reason.

## EF Core

- Read-only queries use `.AsNoTracking()`.
- Use `.TagWithCallSite()` (or the project's equivalent query-tagging helper) on EF queries so slow-query logs are traceable back to source, if the project has adopted this convention.
- Don't compose LINQ that can't translate to SQL (client-side evaluation) without a deliberate reason — check whether the projection needs to move client-side.
- Migrations: never hand-edit generated `.Designer.cs` or `*ModelSnapshot.cs` files — regenerate via the EF CLI.

## Error handling

- Throw specific, named exception types (or the project's established exception hierarchy) rather than generic `Exception`/`InvalidOperationException` for expected failure modes — let unexpected ones bubble as-is.
- Exception/error messages include the relevant entity identifiers, not just a generic description — a message without the ID is not actionable in production logs.
- Match the project's existing error-response shape (e.g. `ProblemDetails`, a custom envelope) — don't introduce a second shape for new endpoints.

## Comments & docs

- No comments describing *what* the code does when the identifier names already say it — only comment the *why*: a non-obvious constraint, a workaround, a subtlety a future reader would trip over.
- XML doc comments on public API surface (controllers, public service methods) describe the contract, not restate the method name.

## Reviewing structure

- One PR = one purpose — flag unrelated formatting, dependency bumps, or drive-by refactors bundled into a feature/bugfix PR.
- Flag business-rule changes gated behind a feature flag when the flag isn't for an actual experiment or per-tenant/per-environment variation — that's usually a sign the change should just ship, or the flag is masking incomplete rollout planning.
