---
description: MSTest / NUnit / xUnit conventions for .NET unit and integration tests
globs: **/*Tests.cs
alwaysApply: false
---

# .NET Testing

## Structure

- Arrange-Act-Assert, with the three sections visually separated (blank line or comment) — don't interleave setup and assertions.
- Test method naming: `MethodUnderTest_Scenario_ExpectedResult` (or match whatever pattern the project's existing tests already use — check a few neighboring files before assuming).
- One behavior per test. A test with multiple unrelated assertions covering different scenarios should be split.

## Mocking

- Prefer constructor-injected interfaces mocked via the project's mocking library (Moq, NSubstitute) over static/singleton reach-arounds.
- Set up only the mock behavior the test actually exercises — don't copy a large shared setup block if the test needs one return value.
- Verify calls (`Verify`/`Received`) only when the call itself is the behavior under test, not as a redundant check alongside a return-value assertion that already proves the same thing.

## Integration tests

- Reset/seed state per test (or per test class) so tests don't depend on execution order.
- Prefer a real database (via Testcontainers or an equivalent disposable instance) over mocking the ORM for anything that exercises actual query behavior — mocked-DB tests can pass while the real query is broken.
- Name test classes/files to match the type under test, with `Tests` as the suffix — don't fold multiple unrelated types' tests into one file.

## What NOT to test

- Don't assert on private/internal state reachable only via reflection — test observable behavior (return values, persisted state, calls to collaborators).
- Don't duplicate a unit test's coverage in an integration test unless the integration adds something the unit test can't verify (real DB behavior, wiring across layers).

## Coverage discipline

- When a business rule changes, audit existing passing tests for that rule — a test that still passes might be asserting the *old* behavior on stale seed data, not confirming the new behavior. Invert the assertion and rename, don't just patch the seed to keep it green.
- New failure-path branches (validation errors, not-found, conflict) get their own test, not just a comment saying they're "similar to X."
