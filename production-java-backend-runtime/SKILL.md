---
name: production-java-backend-runtime
description: Use this skill when building, modifying, reviewing, debugging, or testing production Java backend services, especially Spring Boot services, REST/gRPC APIs, messaging/event-driven flows, persistence, transactions, migrations, microservice contracts, observability, security, resilience, Docker/container behavior, or Kubernetes runtime-facing changes. Use it for codebases that require maintainable, testable, production-safe backend changes with bounded improvement of touched code.
---

# Production Java Backend Runtime Skill

Use this skill for production-grade Java backend work where correctness, maintainability, testability, runtime safety, and bounded improvement matter.

This skill is application-first and platform-aware, but not platform-owner. It applies to Java backend services and their runtime-facing artifacts when those artifacts live in the repository.

## When to use this skill

Use this skill for tasks involving:

- Java backend code.
- Spring Boot applications.
- REST, gRPC, GraphQL, or internal service APIs.
- Messaging, event handlers, consumers, producers, schedulers, jobs, or workers.
- Persistence, repositories, transactions, migrations, queries, indexes, or constraints.
- Microservice contracts, backward compatibility, idempotency, retries, timeouts, or distributed consistency.
- Observability: logs, metrics, traces, correlation IDs, health endpoints, diagnostics.
- Security: authn/authz, tenancy, object ownership, confidential data, sensitive data, runtime privileges.
- Runtime-facing artifacts: Dockerfile, Helm, Kustomize, Kubernetes YAML, probes, resources, env/config.
- Performance/load-sensitive changes: hot paths, high-throughput endpoints, large datasets, query shape, caching, serialization, concurrency, resource limits, JVM memory/GC, or SLO/cost impact.
- Code review, debugging, refactoring, or tests in a Java backend repository.

Do not use this skill as the primary guide for frontend, mobile, data science, generic DevOps/IaC ownership, or non-Java code unless the task directly affects a Java backend service.

## Operating principles

1. Understand before changing.
   Read the task, code, tests, domain vocabulary, configs, docs, and ADRs before editing.

2. Behavior over implementation.
   Preserve observable behavior unless the task explicitly changes it.
   Test through public seams, not private implementation details.

3. Tight feedback loop over speculation.
   Prefer executable proof: failing test, reproduction script, build, static check, integration test, curl, benchmark, or focused diagnostic command.

4. Small vertical slices over broad rewrites.
   Implement one coherent behavior at a time.
   Avoid horizontal “all structure first, all logic later” changes.

5. Deep modules over shallow pass-through layers.
   Prefer small, stable interfaces with meaningful behavior behind them.
   Do not introduce abstractions until there is real variation, leverage, or locality.

6. Domain language over technical noise.
   Use the project’s domain language in names, tests, APIs, events, docs, and summaries.
   Challenge vague or overloaded terms.

7. Production safety over local convenience.
   Consider compatibility, migrations, idempotency, timeouts, retries, resource use, graceful shutdown, health checks, observability, performance/load impact, and rollback impact.

8. Bounded improvement over blind consistency.
   Follow repository conventions when they are reasonable.
   Do not copy local bad practice when a low-risk improvement is possible in touched code.

9. Refactor only with protection.
   Refactor after behavior is understood and preferably covered.
   Keep refactoring local unless broader redesign is explicitly requested.

10. Report verification honestly.
    Final response must say what changed, what was verified, what was not verified, and any residual risks or follow-up refactoring opportunities.

## Required workflow

### 1. Discover evidence before editing

Before editing, build a compact evidence-based understanding of the task.

Prefer reading repository evidence over asking the user:

- Existing code path.
- Existing tests.
- Build files.
- Runtime configuration.
- Migrations.
- API schemas.
- Messaging schemas.
- CI config.
- `AGENTS.md`.
- README and local docs.
- ADRs and domain glossary, if present.

Answer these before implementation when relevant:

- What behavior must change?
- Where is the relevant code path?
- What public contracts are involved?
- What tests already cover this behavior?
- What repository conventions are real and current?
- What runtime/deployment artifacts may be affected?
- What is the smallest safe vertical slice?

Ask the user only when the answer cannot be inferred from the repository or the decision changes product behavior, API contract, data semantics, security posture, performance expectations, or runtime ownership.

### 2. Plan the smallest safe vertical slice

For non-trivial work, form a short plan before editing:

- Desired behavior.
- Files/modules likely affected.
- Feedback loop to prove correctness.
- Tests to add/update.
- Runtime/security/data/contract/performance risks.
- References to load.

Keep the plan compact. Do not create a large design document unless the user asked for one or the change is high risk.

### 3. Implement feedback-loop-first

Prefer test-first for behavior changes and bug fixes.

Use this order:

1. Reproduce or define the expected behavior.
2. Add or update a focused behavioral test when practical.
3. Make the smallest implementation change.
4. Run the narrowest relevant check.
5. Refactor touched code after behavior is protected.
6. Run broader checks when risk justifies it.

For changes that are not practical to test first, state the chosen feedback loop before editing.

### 4. Improve touched code locally

When editing existing poor code:

- Preserve public behavior and compatibility.
- Improve the touched area when the improvement is low-risk and task-relevant.
- Prefer clear names, explicit contracts, smaller methods, better seams, better validation, and simpler control flow.
- Do not perform broad cleanup, mass renames, package moves, or architecture rewrites unless required by the task or explicitly requested.
- Mention broader refactoring opportunities separately.

### 5. Apply conditional deep checks

Always consider whether the change touches:

- Java quality.
- Spring Boot framework behavior.
- Tests and verification.
- Microservice contracts.
- Data, transactions, and migrations.
- Resilience.
- Observability and runtime health.
- Security.
- Architecture boundaries.
- Kubernetes/container runtime.
- Performance/load behavior.

Load only the relevant reference files.

### 6. Verify narrow-first, then broaden

Discover verification commands from:

- `AGENTS.md`.
- README.
- Build files.
- CI config.
- Makefile.
- Gradle/Maven wrappers.
- Existing docs.

Prefer repository wrappers:

- `./gradlew` over `gradle`.
- `./mvnw` over `mvn`.

Default order:

1. Compile/static check for touched module if cheap.
2. Narrow behavior test for changed code path.
3. Relevant integration/slice/contract/migration/performance check.
4. Broader module-level check.
5. Full repo check only when justified or requested.

Never claim checks passed unless they actually ran and passed.

### 7. Final self-review

Before final response, check:

- Correctness.
- Behavior preservation or intended behavior change.
- Backward compatibility.
- Tests.
- Runtime impact.
- Observability.
- Security.
- Data/migration safety.
- Performance/load impact when relevant.
- Refactoring scope.
- Residual risks.

## Reference loading guide

Do not load every reference by default. Load only what the task touches.

- `references/java-code-quality.md`
  - Java source changes, refactoring, naming, complexity, immutability, error handling.

- `references/spring-boot.md`
  - Spring configuration, beans, controllers, clients, Actuator, properties, profiles, startup, framework integration.

- `references/testing.md`
  - Behavior changes, bug fixes, refactoring, missing coverage, flaky tests, verification strategy.

- `references/microservices-contracts.md`
  - HTTP/gRPC APIs, events, schemas, clients, auth contracts, error semantics, backward compatibility.

- `references/data-transactions-migrations.md`
  - DB schema, queries, repositories, transactions, migrations, indexes, constraints, outbox/inbox, event consistency.

- `references/resilience.md`
  - External calls, messaging, retries, timeouts, circuit breakers, rate limits, bulkheads, graceful degradation.

- `references/observability-runtime-health.md`
  - Logs, metrics, traces, correlation IDs, Actuator health, probes, startup/shutdown, jobs, consumers, failure diagnostics.

- `references/security.md`
  - Authentication, authorization, tenancy, confidential data, sensitive data, input validation, admin APIs, or container/Kubernetes security posture.

- `references/clean-architecture-boundaries.md`
  - Module/package boundaries, dependency direction, domain logic placement, ports/adapters, refactoring, architectural cleanup.

- `references/kubernetes-container-runtime.md`
  - Dockerfile, Helm, Kustomize, Kubernetes YAML, probes, resources, security context, env/config, ports, init jobs, deployment behavior.

- `references/performance-load.md`
  - Hot paths, latency-sensitive APIs, high-throughput endpoints, large datasets, query shape, pagination, batching, caching, serialization, allocation-heavy code, concurrency, pools, consumers, backpressure, resource limits, JVM memory/GC, autoscaling signals, or SLO/cost impact.

## Quality gates

Done means:

- Code follows reasonable repository conventions.
- Code does not blindly preserve local bad practices in touched areas.
- Change is minimal and localized unless architecture requires broader refactoring.
- Public contracts remain backward-compatible or the migration path is explicit.
- Error handling, timeouts, retries, idempotency, and transaction boundaries are considered when relevant.
- Tests are added or updated at the right level.
- Relevant build/test/static checks are run when available.
- Runtime impact is reviewed: config, health, observability, resources, startup/shutdown, migrations.
- Security impact is reviewed: auth, ownership, tenancy, confidential data, sensitive data, least privilege.
- Performance/load impact is considered when the change touches hot paths, large data, database query shape, caching, serialization, concurrency, external calls, consumers, JVM/runtime resources, or Kubernetes resource limits.
- Final response reports what changed, what was verified, and what remains unverified.

## Final response contract

Be concise and technical.

Final response must include:

- What changed.
- Why it changed.
- What was verified.
- What was not verified.
- Residual risks or follow-up refactoring opportunities, if any.

Do not explain generic best practices unless they directly justify a decision.
Do not hide failing checks, skipped checks, flaky behavior, or pre-existing issues.
