# Performance and Load

## Decision rules

Do not optimize by intuition alone. First establish why the changed path is performance-sensitive and what evidence is available.

This reference owns boundedness, amplification, saturation, resource pressure, and measurement. Resilience owns whether retries/timeouts/idempotency are semantically correct; data owns transaction/data invariants.

Check the dimensions that can dominate the changed path:

- algorithmic complexity and input cardinality;
- query count, result size, pagination, fetch/batch shape, locks, and connection-pool pressure;
- serialization/parsing and allocation rate;
- blocking calls, lock contention, executor/thread-pool use, and parallelism bounds;
- timeout/retry amplification and downstream saturation;
- cache cardinality, TTL/eviction, stampede, staleness, and tenant isolation;
- consumer throughput, lag, batching, parallelism, and backpressure;
- JVM heap/native memory, GC, container CPU/memory, OOM/restart risk, and autoscaling signals.

Never introduce unbounded result sets, queues, caches, memory accumulation, retries, parallelism, or telemetry cardinality on a path whose input can grow.

Prefer existing production evidence and repository mechanisms: metrics/traces, query plans, load tests, JFR/profiles, JMH where appropriate, CI performance gates, and documented SLOs.

Use JMH only for isolated JVM behavior that a microbenchmark can faithfully represent. It is not production proof for DB/network/contention/GC/container-dependent paths.

## Failure modes

Watch for:

- N+1 queries or `findAll`/full materialization on unbounded data;
- in-memory filtering where DB projection/pagination/streaming is the intended scalable boundary;
- synchronous external calls added to high-QPS paths without latency budget reasoning;
- retries multiplying load during dependency failure;
- ad-hoc thread pools or increased consumer parallelism without downstream/resource bounds;
- cache entries without bounded cardinality/eviction;
- high-cardinality metrics on hot paths;
- repeated expensive parser/mapper/regex construction in tight loops;
- global locks around I/O;
- container memory changes disconnected from JVM heap/native-memory assumptions;
- probe timing likely to flap only under load.

## Verification

Use the narrowest measurement that addresses the actual risk: query plan/repository integration for DB changes, existing benchmark/load test for throughput/latency, JFR/profile for CPU/allocation/GC/blocking/contention, or runtime metrics/resource review for saturation. If meaningful measurement is unavailable, state the specific uncertainty and the measurement that would reduce it; do not claim performance safety beyond the available evidence.
