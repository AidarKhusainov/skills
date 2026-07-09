# Testing

## Load this when

Load this reference for behavior changes, bug fixes, refactoring, missing coverage, flaky tests, test strategy, or verification planning.

## Non-negotiable checks

- Prefer behavioral tests over implementation-detail tests.
- Use the narrowest test that gives meaningful confidence.
- Test through public seams.
- Add regression tests for bugs before or alongside the fix.
- Do not assert private implementation details unless they are the contract.
- Do not add tests that merely assert mocks were called unless that interaction is the contract.
- Do not weaken, delete, or skip tests to make a change pass without clear justification.
- Do not treat compilation as behavior proof.

## Proof map

For non-trivial changes, build this internal map:

```text
changed behavior -> existing proof -> added/updated proof -> remaining gap
```

Use it to choose the smallest useful test level and to report what remains unverified.

Print the map only when it clarifies the final response or a verification tradeoff.

## Preferred approach

Use feedback-loop-first implementation:

1. Identify expected behavior.
2. Find existing tests.
3. Add or update the smallest useful test.
4. Make the implementation pass.
5. Refactor with protection.
6. Run narrow checks first, then broader checks as needed.

Preferred test levels:

- Unit tests for pure domain logic, validation, mapping, policies, edge cases.
- Spring slice tests for web, persistence, serialization, security boundaries.
- Integration tests for database behavior, transactions, messaging, external clients, configuration, and runtime wiring.
- Contract tests for externally consumed APIs/events when the repo supports them.
- E2E tests only when behavior spans deployed boundaries or cannot be trusted lower down.

Use production-like dependencies when substitutes hide important behavior:

- Real database via Testcontainers when SQL dialect, transactions, indexes, locking, JSON, time zones, or migrations matter.
- Real broker/container when message semantics, serialization, ordering, retries, or consumer lifecycle matter.

## Red flags

- Tests coupled to private methods.
- Over-mocking domain behavior.
- `@SpringBootTest` used for simple pure logic.
- In-memory DB used where production SQL behavior matters.
- No negative tests for authorization/security-sensitive paths.
- Bug fix without regression test.
- Snapshot/golden tests that obscure intended behavior.
- Flaky sleeps instead of deterministic synchronization.
- Tests requiring local environment secrets.

## Verification

Use this order by default:

1. Narrow affected test.
2. Touched module compile/static check.
3. Relevant slice/integration/contract/migration test.
4. Broader module-level check.
5. Full repository check when justified or requested.

When checks fail:

- Identify whether failure is related.
- Capture exact failing command.
- Do not hide failures.
- Do not claim success.
- If likely pre-existing, provide evidence.

## Final response notes

Mention:

- Tests added or updated.
- Exact commands run.
- Failed/skipped checks.
- Unverified behavior and why.
