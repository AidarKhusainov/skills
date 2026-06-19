# Kubernetes and Container Runtime

## Load this when

Load this reference for Dockerfiles, container entrypoints, Helm, Kustomize, Kubernetes YAML, probes, resources, security context, env/config, service ports, init jobs, migration jobs, deployment behavior, or runtime lifecycle.

## Non-negotiable checks

Treat these as runtime contracts:

- Dockerfile.
- Entrypoint and command.
- Kubernetes manifests.
- Helm/Kustomize values.
- Readiness/liveness/startup probes.
- Resource requests and limits.
- Security context.
- Environment variables.
- Mounted config.
- Service ports.
- Shutdown behavior.
- Migration/init jobs.
- Consumers/workers lifecycle.

Do not change runtime-facing artifacts without checking:

- Rollout behavior.
- Compatibility.
- Health.
- Observability.
- Security.
- Resource impact.

## Preferred approach

For probes:

- Readiness means safe to receive traffic.
- Liveness means unhealthy enough to restart.
- Startup probes protect slow-starting services.
- Do not make liveness fail because of transient downstream issues.
- Prefer startup probe over weakening liveness for slow startup.

For resources:

- Keep requests/limits explicit when repo owns manifests.
- Consider CPU, memory, startup, GC, thread pools, connection pools.
- Do not remove limits/requests casually.
- Mention cluster-level assumptions if not visible.

For shutdown:

- Respect Kubernetes termination grace period.
- Ensure application graceful shutdown is enabled/configured when repo owns it.
- Stop accepting new work.
- Let in-flight requests/messages finish or safely abandon.
- Ensure message consumers, schedulers, and workers stop predictably.

For security context:

- Prefer non-root.
- Prefer least privilege.
- Drop unnecessary capabilities.
- Avoid privileged containers.
- Avoid host namespaces.
- Avoid writable root filesystems when compatible.
- Avoid broad mounted confidential data and overbroad service accounts.

For config:

- Treat env/config keys as contracts.
- Avoid logging config values that may contain confidential data.
- Preserve profile/environment-specific behavior.

## Red flags

- Readiness is just “process started”.
- Liveness depends on DB/broker/third-party.
- Probe path changed without application support.
- Container port mismatch.
- Resource limits removed.
- Security context weakened.
- Confidential data mounted broadly.
- Migration runs in main app container without rollout consideration.
- Consumer ignores termination.
- Dockerfile runs as root without justification.
- Entrypoint change bypasses JVM options or signal handling.

## Verification

Prefer:

- Manifest render: Helm/Kustomize/template command.
- YAML validation if repo provides it.
- Docker build if relevant and cheap.
- Application startup check if available.
- Health endpoint check if available.
- Tests for shutdown/consumer lifecycle when practical.

## Final response notes

Mention:

- Runtime artifacts changed.
- Probe/resource/security assumptions.
- Rollout risk.
- Runtime checks run or not run.
