# Observability and Runtime Health

## Load this when

Load this reference for logs, metrics, traces, correlation IDs, Actuator health, readiness/liveness/startup probes, startup/shutdown, jobs, consumers, failure diagnostics, or production troubleshooting.

## Non-negotiable checks

Treat these as production behavior:

- Logs.
- Metrics.
- Traces.
- Correlation/request IDs.
- Health endpoints.
- Readiness/liveness/startup behavior.
- Startup and shutdown behavior.
- Failure diagnostics.

Every meaningful failure path should produce actionable diagnostics:

- What failed.
- Where it failed.
- Why, if known.
- Safe identifiers needed for debugging.

Never log:

- Secrets.
- Credentials.
- Tokens.
- PII.
- Payment data.
- Sensitive payloads.

## Preferred approach

For logs:

- Use structured logs where repository supports them.
- Include safe correlation/domain identifiers.
- Avoid ambiguous free-text-only logs for important events.
- Avoid log spam in hot paths.
- Preserve stack traces where useful.

For metrics:

- Add/update metrics for important business flows.
- Track external dependencies, retries, timeouts, queue lag, failures, saturation.
- Use low-cardinality tags.
- Avoid user IDs, raw URLs, UUIDs, emails, or unbounded values as labels.

For tracing:

- Preserve trace/correlation context across inbound requests, outbound calls, async jobs, and message handlers.
- Add spans around high-value operations when repo uses tracing.

For health:

- Readiness means safe to receive traffic.
- Liveness means the process is unhealthy enough to restart.
- Do not make liveness fail because of transient downstream failures.
- Use startup probes for slow-starting services before weakening liveness/readiness.
- Health checks should reflect runtime readiness, not just process existence.

For shutdown:

- Stop accepting new work.
- Let in-flight work finish or safely abandon.
- Ensure consumers/jobs/schedulers stop predictably.
- Respect Kubernetes termination grace assumptions.

## Red flags

- Catching and logging error without useful context.
- Logs contain secrets or sensitive payloads.
- Metrics with high-cardinality labels.
- Readiness returns OK before required resources/config are initialized.
- Liveness depends on external DB, broker, or third-party service.
- Shutdown ignores in-flight requests or messages.
- Consumer continues taking messages after shutdown starts.
- Startup is slow but no startup probe exists.

## Verification

Prefer:

- Tests for failure-path diagnostics when practical.
- Health endpoint tests.
- Configuration tests for probe endpoints.
- Consumer shutdown/lifecycle tests.
- Manual verification notes for runtime behavior not testable locally.

## Final response notes

Mention:

- Observability added or preserved.
- Health/probe assumptions.
- Runtime lifecycle assumptions.
- Diagnostics that remain unverified.
