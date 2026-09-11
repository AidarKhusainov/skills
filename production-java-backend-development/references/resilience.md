# Resilience and Distributed Failure

## Decision rules

Treat every network, broker, cache, storage, database-under-load, and third-party boundary as fallible.

This reference owns timeout, retry, idempotency, and failure-recovery semantics. Performance owns their amplification/saturation impact under load.

- Every synchronous external call must have an explicit timeout or inherit a verified bounded default.
- Retry only failures that are plausibly transient and operations that are safe to repeat.
- Keep retries bounded; use appropriate backoff/jitter where synchronized retries could amplify failure.
- Do not retry non-idempotent commands unless an idempotency key, deduplication, transactional mechanism, or equivalent safety property makes repetition correct.
- Preserve failure meaning across boundaries; do not turn dependency failure or partial completion into misleading success.
- Use circuit breakers, bulkheads, rate limits, and degradation only when they correspond to a concrete failure mode and defined fallback/recovery behavior.
- Assume at-least-once message delivery unless repository/platform evidence guarantees stronger semantics.
- Consumers should make acknowledgement/commit ordering, duplicate handling, poison-message behavior, and shutdown semantics explicit.

## Failure modes

Watch for:

- missing/unbounded timeouts;
- infinite or multiplicative retries;
- retrying commands with externally visible side effects without idempotency;
- fallback that silently loses or fabricates data;
- blocking I/O on event-loop/reactive execution;
- shared executor/connection pools with no saturation isolation for critical flows;
- consumer acknowledgement before durable side effects;
- poison messages causing endless hot-loop retries;
- shutdown that continues taking work after the service should drain.

## Verification

Exercise timeout/error mapping, retry bounds, idempotency/duplicate delivery, acknowledgement/commit behavior, and fallback/degradation paths at the narrowest level that preserves real semantics. Use integration tests when framework/client/broker configuration is part of the behavior.
