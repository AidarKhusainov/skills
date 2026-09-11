# Observability and Runtime Health

## Decision rules

Observability should make changed production behavior diagnosable without exposing sensitive data or creating unbounded telemetry cost.

This reference owns application health semantics. Kubernetes/container configuration should represent those semantics rather than redefine them.

### Logs and diagnostics

- Important failure paths should expose what failed, where, and safe identifiers/correlation needed for diagnosis.
- Preserve useful exception causes/stack traces unless the boundary intentionally translates them.
- Do not log secrets, credentials, tokens, sensitive payloads, or unnecessary personal data.
- Avoid noisy per-item logs on hot paths unless sampling/rate control makes them operationally useful.

### Metrics and traces

- Use bounded-cardinality labels/tags; do not use user IDs, UUIDs, raw URLs, emails, or other unbounded values as metric dimensions.
- Preserve request/trace/correlation context across inbound requests, outbound calls, async work, and message handling when the stack supports it.
- Instrument high-value dependencies/failure paths when existing service conventions rely on those signals for operation.
- Treat metric names and bounded tag semantics consumed by dashboards/alerts as operational interfaces; change them deliberately when operators depend on them.

### Health and lifecycle

- Readiness means the instance can safely receive its intended traffic/work.
- Liveness means the process is unhealthy enough that restart is an appropriate recovery action; transient downstream failure alone should not normally fail liveness.
- Startup semantics should distinguish slow initialization from permanent unhealthiness.
- Shutdown should stop admission of new work and allow in-flight work to complete or be safely abandoned according to the service contract.

## Failure modes

Watch for:

- swallowed failures with no actionable diagnostics;
- secrets/sensitive payloads in logs, traces, or metric labels;
- unbounded metric cardinality;
- readiness reporting success before required initialization completes;
- liveness coupled directly to DB/broker/third-party availability;
- trace/correlation loss across async/message boundaries;
- consumers/jobs continuing to accept work during shutdown.

## Verification

Verify changed health/diagnostic semantics through endpoint/configuration/lifecycle tests where practical. For observability that cannot be asserted cheaply, inspect the emitted fields/tags and state the remaining runtime-only uncertainty rather than fabricating local proof.
