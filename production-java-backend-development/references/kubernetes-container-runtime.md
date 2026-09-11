# Kubernetes and Container Runtime

## Decision rules

Apply this reference only to runtime artifacts owned with the Java service. Generic platform/IaC ownership belongs elsewhere.

This reference owns how container/deployment configuration represents application lifecycle, health, resources, configuration, and workload privilege. Application health semantics belong to observability/runtime-health; auth/data trust rules belong to security.

### Probes and lifecycle

- Configure readiness/liveness/startup probes to match the application's actual health semantics.
- Prefer a startup probe for legitimately slow initialization rather than weakening liveness/readiness semantics.
- Keep probe paths/ports consistent with application exposure and security configuration.
- Preserve graceful shutdown: signal delivery, termination grace period, stopped admission of new work, and predictable consumer/job shutdown.

### Resources and JVM/container behavior

- Treat CPU/memory requests/limits as runtime assumptions that interact with JVM heap/native memory, GC, thread pools, connection pools, startup, and throttling.
- Do not remove or materially change resource controls without evidence about the service behavior/ownership model.
- Keep container entrypoints and signal handling compatible with JVM options and graceful termination.

### Configuration and workload security

- Treat env/config key names and required mounts as deployment interfaces; preserve environment/profile semantics.
- Keep secrets narrowly mounted/exposed and never log their values.
- Prefer non-root and least-privilege workload/container settings; avoid privileged mode, host namespaces, unnecessary capabilities, writable root filesystem, or broad service-account permissions unless the service requirement justifies them.

## Failure modes

Watch for:

- readiness that only proves the process started;
- liveness depending directly on transient downstream health;
- probe path/port mismatch;
- entrypoint changes that break JVM options or signal propagation;
- resource limits changed without JVM/runtime impact review;
- migration/init work moved into the main container lifecycle without rollout reasoning;
- consumers ignoring termination;
- broadened secret mounts/service-account permissions;
- root/privileged/capability escalation used to work around an application permission problem.

## Verification

Render Helm/Kustomize/templates with repository commands when changed, validate resulting manifests, build the image when container construction changed, and exercise startup/health/shutdown behavior when practical. Review resource/security-context deltas even when no local cluster is available.
