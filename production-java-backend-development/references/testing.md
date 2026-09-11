# Backend Testing and Proof Obligations

This reference selects the proof level needed for production Java/Spring backend semantics. It does not define TDD sequencing or the host workflow's generic completion-verification process.

## Decision rules

Prove observable behavior at the cheapest level that still exercises the semantics at risk.

For a material backend change, reason from:

```text
semantic risk -> observable invariant -> cheapest faithful boundary -> proof -> remaining gap
```

- Use unit tests for pure domain policy, deterministic transformations, and value semantics when framework/runtime behavior is irrelevant.
- Use Spring slices when the risk is owned by a focused framework boundary such as MVC validation/serialization, persistence mapping, or configuration wiring and the slice faithfully includes the relevant machinery.
- Use integration tests when database, transaction, locking, serialization, messaging, client, configuration, lifecycle, or multi-component semantics are part of the claim.
- Use production-like dependencies when substitutes hide the property under test: for example, the actual database engine for dialect/locking/migration semantics or a real broker/container when delivery, serialization, ordering, or lifecycle behavior matters.
- Test externally consumed API/event/message behavior against the actual serialized contract, status/error semantics, compatibility rules, and required/optional fields rather than only internal DTO construction.
- For transaction or persistence changes, prove the relevant commit/rollback, isolation, constraint, query, cascade, or consistency behavior instead of relying only on repository mocks.
- For retries, idempotency, duplicate delivery, outbox/inbox, schedulers, or consumers, prove the state transition and side-effect behavior across the failure/retry boundary that can occur in production.
- For authentication, authorization, tenancy, ownership, and sensitive-data changes, include denial/isolation cases for the concrete trust boundary being changed.
- For migrations, validate the migration chain and use representative production-like data/engine behavior when locks, backfills, constraints, indexes, defaults, or rollout compatibility are material.
- For performance-sensitive paths, use measurement that can support the actual claim: representative dataset/query plan, benchmark, profile, load test, allocation evidence, or resource/saturation observation as appropriate.
- Keep asynchronous tests deterministic; prefer synchronization, controllable clocks, latches, or eventual assertions over arbitrary sleeps.
- Assert observable behavior and invariants. Mock-interaction assertions are appropriate only when the interaction itself is the contract or the only faithful observable seam.

## Proof selection examples

| Risk | Proof that can establish it |
| --- | --- |
| Pure domain invariant | Unit test through the domain/application seam |
| Bean wiring or Spring configuration | Focused context/slice test |
| Jackson/HTTP validation and error shape | MVC/HTTP boundary test against serialized output |
| JPA mapping/query semantics | Persistence integration test with the relevant database behavior |
| Transaction rollback/locking/isolation | Integration test exercising the real transaction/database boundary |
| Flyway/Liquibase migration | Migration validation plus production-like DB checks when semantics are engine/data dependent |
| API/event compatibility | Contract or boundary test over the externally visible representation |
| Retry/idempotency/duplicate delivery | Failure-path integration test over state transition and side effects |
| Authorization/tenancy/ownership | Positive and negative boundary tests including cross-tenant/object denial |
| Kubernetes probe/runtime semantics | Runtime/deployment check that exercises the represented application health behavior |
| Latency/throughput/allocation claim | Representative measurement rather than functional tests alone |

## Failure modes

Watch for:

- private-method tests that prove implementation shape instead of behavior;
- tests that mirror branches without establishing the externally meaningful invariant;
- excessive mocking of domain policy or infrastructure whose real semantics are the subject of the change;
- in-memory databases masking production SQL, locking, constraint, or migration behavior;
- Spring tests that bypass the proxy, serialization, validation, or configuration mechanism under change;
- happy-path-only security tests that miss tenant/object ownership denial;
- broker/client stubs that cannot reproduce the delivery, retry, timeout, or serialization property being claimed;
- flaky timing assumptions and arbitrary sleeps;
- snapshots/golden files that obscure the semantic assertion;
- tests requiring developer-local secrets or mutable external state;
- performance conclusions inferred from correctness tests without representative measurement.

## Residual uncertainty

When the faithful environment or boundary cannot be exercised, state the unverified backend semantic explicitly. Do not substitute a cheaper test that cannot prove the property and then treat the risk as closed.
