# Runtime, Resilience, and Concurrency Review Reference

Load this file only when the changed surface touches Kubernetes, Helm, manifests, config/env, probes, resources, shutdown, multi-pod behavior, external calls, retries, timeouts, circuit breakers, async jobs, consumers, schedulers, idempotency, duplicate delivery, locking, or race-prone state transitions.

## Review focus

Check whether the PR preserves:
- safe startup and shutdown;
- rolling deployment safety;
- multi-replica correctness;
- bounded external dependency failure;
- retry and timeout correctness;
- idempotency under retries and duplicate delivery;
- concurrency safety;
- operational diagnosability of runtime failures.

## State-transition artifact

For async, scheduled, consumer, retry, lock-sensitive, or multi-step runtime changes, build this internal view before reporting findings:

```text
trigger -> read state -> lock/constraint -> write state -> side effect -> ack/commit -> retry result
```

Use it to check duplicate delivery, concurrent execution, partial failure, stale state, retry after timeout, shutdown during work, and cross-pod behavior.

Print the view only in audit mode or when it is needed as evidence for a finding/question.

## Kubernetes and runtime manifests

Report when the PR:
- adds runtime behavior without required config/env/Helm wiring;
- changes config names, defaults, or secrets without deployment compatibility;
- changes container port, path, profile, or command without manifest updates;
- changes memory/CPU behavior without checking requests/limits where the project manages them;
- adds startup dependency but does not consider startup/readiness behavior;
- changes health endpoint behavior without probe compatibility;
- makes pods receive traffic before required dependencies/config/migrations are ready;
- makes liveness fail for dependency outages that should not restart the pod;
- changes shutdown behavior without drain/graceful termination reasoning.

## Probes

Report when:
- readiness does not reflect ability to safely serve traffic;
- liveness can kill a healthy-but-dependent-on-failing-external-service pod;
- startup time increases but startup probe or initial delay is not adjusted;
- probe endpoint requires auth or depends on unstable downstream services without intent;
- probe path/status changes but manifest is not updated;
- long migration/cache warmup/indexing blocks readiness without operational plan.

Use Kubernetes / Runtime when traffic routing, pod lifecycle, or deployment behavior is the main merge risk.

## Resources and capacity

Report when the PR:
- adds memory-heavy/cache/batch behavior without resource or backpressure reasoning;
- adds CPU-heavy work on request path without latency/capacity evidence;
- changes concurrency/thread pools without queue/backpressure limits;
- adds large payload buffering or unbounded collections;
- adds high-cardinality metrics/log labels that can create telemetry cost or overload;
- changes resource settings in a way that can cause throttling, OOM, or unschedulable pods.

Do not report resource preference without changed runtime behavior or project convention evidence.

## External dependencies

Report when the PR:
- calls external services without explicit timeout;
- retries non-idempotent operations without idempotency key or compensation;
- retries all failures without classifying retryable vs non-retryable outcomes;
- blocks request threads on slow dependencies without fallback/backpressure plan;
- swallows dependency failures and returns success;
- maps dependency failure to wrong domain/API outcome;
- changes client auth/TLS/headers without secret/config update;
- adds dependency on critical path without metrics/tracing/error mapping.

Required change should state timeout, retry, error mapping, and idempotency behavior.

## Consumers, jobs, and schedulers

Report when the PR:
- assumes single pod execution in a multi-replica deployment;
- lacks durable idempotency for at-least-once message delivery;
- processes duplicate events as new state transitions;
- commits offset/acknowledges message before durable state is safe;
- retries poison messages indefinitely without DLQ/quarantine/stop condition;
- runs scheduled jobs concurrently across pods without lock/partitioning/idempotency;
- does not handle shutdown while processing in-flight work;
- loses correlation between event, state change, and emitted output.

Tests should prove duplicate delivery, retry, failure, and concurrent execution behavior for risky paths.

## Idempotency

Report when the PR:
- uses in-memory deduplication for cross-pod or restart-sensitive flows;
- deduplicates by unstable payload or timestamp instead of stable operation/event key;
- performs irreversible side effects before idempotency record is durable;
- checks idempotency outside the transaction that commits the state change;
- treats provider idempotency and local idempotency as interchangeable when both are needed;
- does not define idempotency scope: event, command, business operation, or external call.

Required change should specify durable key, transactional boundary, and expected duplicate outcome.

## Concurrency and races

Report when the PR:
- performs read-check-write without lock, constraint, compare-and-set, or transaction isolation where concurrent callers can violate invariants;
- relies on Java synchronization for state shared across pods or processes;
- updates state based on stale reads;
- changes optimistic/pessimistic locking behavior without conflict handling;
- introduces non-thread-safe shared mutable state in singleton Spring beans;
- uses async execution without context propagation, error handling, or lifecycle control;
- creates race between DB state, cache state, and emitted events;
- changes cache invalidation in a way that can expose stale authorization or domain decisions.

Use Concurrency when the primary risk is simultaneous execution corrupting correctness.

## Resilience patterns

Report when:
- circuit breaker/bulkhead/rate limiter settings do not match dependency risk;
- fallback hides data corruption, failed payment, failed authorization, or failed persistence;
- fallback returns stale data where stale data is unsafe;
- retry storm is possible under dependency outage;
- timeout exceeds caller budget or transaction lifetime;
- error classification loses retryability semantics.

Do not require resilience framework usage when simple timeout/error mapping is sufficient.

## Negative space

For runtime/resilience/concurrency changes, check missing:
- Helm/env/config changes;
- readiness/liveness/startup probe update;
- resource requests/limits review;
- graceful shutdown/drain handling;
- timeout/retry/error mapping tests;
- duplicate-delivery/idempotency tests;
- concurrent execution tests;
- DLQ/poison-message handling;
- metrics/logs/traces for new critical runtime path.

## Findings to avoid

Do not report:
- generic Kubernetes best practices with no changed runtime surface;
- theoretical race without realistic concurrent path;
- idempotency requirement for purely read-only safe operations;
- broad resilience framework request where local timeout/error mapping solves the risk;
- unrelated legacy runtime issues not touched or amplified by the PR.
