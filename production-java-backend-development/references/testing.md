# Testing and Verification

## Decision rules

Tests should prove observable behavior at the cheapest level that still exercises the semantics at risk.

For a non-trivial change, reason from:

```text
changed behavior -> existing proof -> added/updated proof -> remaining gap
```

- Prefer tests through public/use-case seams over private implementation details.
- A bug fix should normally gain regression proof unless another deterministic check already covers the exact failure.
- Do not assert mock calls unless the interaction itself is the contract.
- Do not weaken, delete, skip, or over-mock tests merely to make a change pass.
- Prefer a unit test for pure policy and deterministic logic; use Spring slices for framework boundaries; use integration tests when database, transaction, serialization, messaging, configuration, or client semantics matter.
- Use production-like dependencies when substitutes hide the behavior under change: e.g. a real DB for dialect/locking/migration semantics or a real broker/container for delivery/serialization/lifecycle semantics.
- Keep asynchronous tests deterministic; avoid sleeps when synchronization or eventual assertions can express the condition.

## Failure modes

Watch for:

- private-method tests;
- tests that only mirror implementation branches without proving behavior;
- excessive mocking of domain policy;
- in-memory DBs masking production SQL behavior;
- missing negative authorization/tenancy cases on security-sensitive changes;
- flaky timing assumptions;
- snapshot/golden tests that obscure the intended semantic assertion;
- tests requiring developer-local secrets or mutable external state.

## Verification

When a check fails, capture the exact command and determine whether the failure is caused by the change. Call a failure pre-existing only when repository evidence supports that conclusion. Never convert an unexecuted or failing check into a success claim.
