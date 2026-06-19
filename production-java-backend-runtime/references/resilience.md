# Resilience

## Load this when

Load this reference for external calls, messaging, retries, timeouts, circuit breakers, rate limits, bulkheads, caches, object storage, third-party APIs, graceful degradation, or cascading failure risk.

## Non-negotiable checks

Treat every external boundary as unreliable:

- Network calls.
- REST/gRPC clients.
- Message broker operations.
- Cache calls.
- Object storage calls.
- Database calls under load.
- Third-party APIs.
- Internal services.

Every synchronous external call must have an explicit timeout or inherit a verified default.

Retries must be bounded and safe for operation semantics.

Do not retry non-idempotent operations unless idempotency keys, deduplication, or another safety mechanism exists.

## Preferred approach

For external calls:

- Set/connect/read/request timeouts.
- Use bounded retries.
- Use backoff and jitter when appropriate.
- Avoid retry storms.
- Preserve correlation IDs.
- Map errors deliberately.
- Add metrics/tracing for important dependencies.
- Prefer explicit failure over hidden partial failure.

Use circuit breakers, rate limiters, and bulkheads when:

- Downstream failures can cascade.
- Latency can exhaust threads/connections.
- A dependency is critical or high volume.
- A shared pool can be saturated.
- Fallback/degradation behavior exists or can be explicit.

For messaging:

- Assume at-least-once delivery unless proven otherwise.
- Make consumers idempotent.
- Handle poison messages.
- Preserve observability for retries/dead letters.
- Stop accepting new work during shutdown.

## Red flags

- Infinite or unbounded retries.
- Retrying POST/commands without idempotency.
- No timeout on external client.
- Blocking calls inside event loop/reactive pipeline.
- Swallowing external failure and returning misleading success.
- Fallback that hides data loss.
- Missing metrics for critical dependency.
- Shared executor/pool with no saturation awareness.
- Circuit breaker added without understanding failure semantics.

## Verification

Prefer:

- Unit tests for retry/idempotency/error mapping.
- Integration tests for client configuration.
- Contract tests for downstream error semantics.
- Failure-path tests.
- Timeout configuration tests where practical.
- Consumer duplicate-message tests.

## Final response notes

Mention:

- Timeout/retry/idempotency behavior.
- Fallback/degradation behavior.
- Any resilience mechanism added or intentionally not added.
- Residual risk.
