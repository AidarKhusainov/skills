# Performance and Load

## Load this when

Load this reference for hot paths, latency-sensitive APIs, high-throughput endpoints, large datasets, database query shape changes, pagination, batching, caching, serialization, allocation-heavy code, concurrency, thread pools, connection pools, consumers, backpressure, resource limits, JVM memory/GC-sensitive changes, autoscaling signals, or changes that may affect SLOs/cost under load.

## Non-negotiable checks

- Do not optimize blindly.
- Preserve correctness, readability, and maintainability.
- First identify whether the changed path is performance-sensitive.
- Prefer evidence over intuition: existing metrics, profiles, benchmarks, logs, traces, query plans, load tests, or production incidents.
- Do not introduce unbounded memory, retries, queues, result sets, parallelism, caches, or metric cardinality.
- Do not hide performance risk just because functional tests pass.
- Report performance-sensitive behavior that could not be measured locally.

## Preferred approach

For hot paths, check:

- Algorithmic complexity.
- Query count.
- Payload size.
- Allocation rate.
- Repeated parsing/serialization.
- Blocking calls.
- Lock contention.
- Thread-pool usage.
- Connection-pool usage.
- Retry amplification.
- Cache cardinality and eviction.
- Metrics label cardinality.
- Backpressure behavior.
- JVM memory and GC assumptions.

For database-heavy paths, check:

- N+1 queries.
- Missing pagination.
- Large result sets loaded into memory.
- Query plan/index assumptions.
- Transaction duration.
- Row/table locks.
- Batch size.
- Fetch size.
- Write amplification.
- Connection-pool pressure.

For external calls, check:

- Timeout budget.
- Retry budget.
- Backoff/jitter.
- Idempotency.
- Circuit breaker/bulkhead need.
- Downstream saturation.
- Tail latency impact.
- Correlation IDs and dependency metrics.

For consumers/jobs, check:

- Throughput.
- Queue lag.
- Batch size.
- Retry/dead-letter behavior.
- Idempotency.
- Downstream saturation.
- Parallelism limits.
- Graceful shutdown.
- Poison-message handling.

For caching, check:

- Cache key cardinality.
- Eviction/TTL.
- Staleness semantics.
- Memory growth.
- Stampede risk.
- Tenant/user isolation.
- Invalidation behavior.

For Kubernetes/container runtime, check:

- CPU requests/limits.
- Memory requests/limits.
- JVM heap/container memory assumptions.
- Startup time.
- GC pressure.
- OOM/restart risk.
- Autoscaling signals.
- Probe timing under load.
- Thread/connection pool sizing relative to pod resources.

## Measurement policy

Prefer existing repository mechanisms:

- Unit/performance tests.
- JMH benchmarks.
- Load tests.
- Profiling scripts.
- Query-plan tooling.
- Actuator/Micrometer metrics.
- JFR profiles.
- CI performance gates.
- Dashboard/alert references in docs.

Use JMH for isolated JVM microbenchmarks only when the code path is suitable for microbenchmarking.

Do not treat a microbenchmark as proof of production performance when the real behavior depends on:

- Database.
- Network.
- Serialization.
- GC under service load.
- Thread contention.
- Connection pools.
- JIT warmup profile.
- Downstream latency.
- Kubernetes CPU throttling.
- Real payload distribution.

If no performance check exists, state the narrowest useful measurement that should be added.

## Red flags

- N+1 queries.
- Unbounded `findAll`, `collect(toList())`, or in-memory filtering over large datasets.
- Loading full payloads when pagination/streaming/projection would suffice.
- Blocking calls added to reactive/event-loop code.
- Synchronous external call added to a high-QPS path.
- Retry without backoff/jitter.
- Retry of non-idempotent operation.
- Cache without TTL/eviction/cardinality control.
- High-cardinality metric labels.
- Large object allocation in tight loops.
- Regex/date/object mapper creation in hot paths.
- Global lock or synchronized block around I/O.
- Thread pool created ad hoc.
- Connection pool defaults changed without evidence.
- Consumer parallelism increased without downstream capacity check.
- Kubernetes memory limit changed without JVM memory review.
- Liveness/readiness behavior likely to flap under load.

## Verification

Prefer, in order:

1. Existing narrow performance test or benchmark.
2. Relevant unit/integration test proving no N+1 or unbounded behavior.
3. Query plan or repository integration test for DB-heavy changes.
4. Existing load test or service-level benchmark.
5. JFR/profile evidence for CPU, allocation, GC, blocking, or lock contention.
6. Actuator/Micrometer metric review for dependency latency, errors, queue lag, and saturation.
7. Kubernetes manifest/resource review for CPU/memory/probe impact.

If verification is not possible locally, report:

- What risk exists.
- Why it could not be measured.
- What measurement would reduce uncertainty.
- Whether the change is safe to merge without that measurement.

## Final response notes

Mention:

- Why the path is or is not performance-sensitive.
- Any performance risks found.
- Any measurement/check run.
- Any performance check not run.
- Any follow-up measurement or benchmark recommended.
