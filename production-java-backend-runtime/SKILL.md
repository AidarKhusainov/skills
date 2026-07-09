---
name: production-java-backend-runtime
description: Use this skill for implementation, debugging, testing, refactoring, and self-review of production Java backend services, especially Spring Boot services, REST/gRPC APIs, messaging/event-driven flows, persistence, transactions, migrations, microservice contracts, observability, security, resilience, performance/load-sensitive paths, Docker/container behavior, or Kubernetes runtime-facing changes. For merge-gate PR/code-diff review, use `strict-backend-code-review` instead.
---

# Production Java Backend Runtime Skill

Use this skill for production-grade Java backend work where correctness, maintainability, testability, runtime safety, and bounded improvement matter.

This skill is application-first and platform-aware, but not platform-owner. It applies to Java backend services and their runtime-facing artifacts when those artifacts live in the repository.

This skill may self-review implementation work, but it is not the primary merge-gate PR/code-diff review skill. For review-only PR/code-diff verdicts, use `strict-backend-code-review`.

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
- Runtime-facing artifacts: Dockerfile, Helm, Kustomize, Kubernetes YAML, probes, resources, env/config/secrets.
- Performance/load-sensitive changes: hot paths, high-throughput endpoints, large datasets, query shape, caching, serialization, concurrency, resource limits, JVM memory/GC, or SLO/cost impact.
- Implementation self-review, debugging, refactoring, or tests in a Java backend repository.

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
   Avoid horizontal "all structure first, all logic later" changes.

5. Deep modules over shallow pass-through layers.
   Prefer small, stable interfaces with meaningful behavior behind them.
   Do not introduce abstractions until there is real variation, leverage, or locality.

6. Domain language over technical noise.
   Use the project's domain language in names, tests, APIs, events, docs, and summaries.
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
    Never claim checks passed unless they ran and passed. Surface skipped, failed, flaky, or unavailable checks.

## Surface classifier

Before planning implementation, classify changed or implied surfaces.

A triggered surface is actionable and must not be silently ignored.

One file can trigger multiple surfaces.

- Java/Spring:
  Java source, Spring controllers/services/configuration, validation, transactions, JPA, serialization, clients, or framework lifecycle behavior.

- Tests:
  behavior changes, bug fixes, refactoring, missing regression proof, flaky tests, or verification strategy.

- API/contracts:
  REST/gRPC/GraphQL, OpenAPI/protobuf, public DTOs, events, schemas, generated clients, error semantics, compatibility, or externally observed behavior.

- Data/migrations:
  repositories, queries, transactions, schema, migrations, indexes, constraints, backfills, outbox/inbox, or data consistency.

- Security/privacy:
  authn/authz, ownership, tenancy, roles/scopes, secrets, confidential data, sensitive data, admin operations, or denied behavior.

- Runtime/resilience:
  external calls, retries, timeouts, circuit breakers, rate limits, bulkheads, graceful degradation, consumers, schedulers, jobs, idempotency, duplicate delivery, or failure recovery.

- Observability/runtime-health:
  logs, metrics, traces, correlation/request IDs, audit logs, health endpoints, diagnostics, alert/dashboard-facing metrics, or failure visibility.

- Kubernetes/container runtime:
  Dockerfile, Helm, Kustomize, Kubernetes YAML, probes, resources, security context, env/config/secrets, ports, init jobs, or deployment behavior.

- Architecture boundaries:
  module/package boundaries, dependency direction, domain/application/infrastructure placement, ports/adapters, use-case boundaries, broad refactor, or architectural cleanup.

- Performance/load:
  hot paths, large datasets, query shape, caching, serialization, allocation-heavy code, pools, backpressure, JVM memory/GC, resource limits, autoscaling signals, SLO/cost impact.

## Risk tier

Before editing, classify the task:

- Trivial:
  local rename, formatting-neutral cleanup, small test-only change, or no behavior/runtime/contract/data/security impact.

- Normal:
  localized behavior change with clear tests and limited affected surface.

- High-risk:
  public contract, persistence/migration, transaction boundary, auth/security, tenant ownership, async/consumer/job behavior, external calls, observability/runtime-health behavior, Kubernetes/runtime config, performance/load-sensitive path, architecture boundary change, or broad refactor.

For high-risk work:
- build the relevant implementation artifacts;
- load every reference required by the triggered surfaces;
- prefer test/reproduction before implementation;
- run the most relevant narrow verification for each high-risk triggered surface when available;
- do not treat compile/static checks as sufficient proof for behavior, contract, data, security, runtime, observability, or performance changes;
- if a meaningful check cannot be run, report the unverified risk explicitly.

## Implementation artifacts

Build compact artifacts internally when the corresponding surface is triggered. Do not print them by default.

- Behavior map:
  `entry point -> validation -> auth -> domain decision -> transaction -> persistence -> side effect -> response/error`

- Change map:
  `requirement -> touched file -> behavior changed -> test/verification`

- Contract map:
  `contract element -> old behavior -> new behavior -> compatibility strategy -> tests`

- Data map:
  `state/invariant -> transaction boundary -> query/schema/migration -> rollout/rollback impact -> tests`

- Runtime map:
  `trigger -> runtime path -> timeout/retry/idempotency/failure recovery -> verification`

- Observability map:
  `event/failure path -> log/metric/trace -> correlation -> cardinality/sensitive-data risk -> verification`

- Kubernetes/container map:
  `runtime artifact -> startup/readiness/shutdown -> resources/security/env/ports -> rollout impact -> verification`

- Architecture map:
  `boundary/change -> dependency direction -> domain/application/infrastructure placement -> scope/protection`

- Security map:
  `caller -> identity -> permission/scope -> ownership/tenancy -> allowed/denied behavior -> tests`

- Performance map:
  `hot path -> data size/cardinality -> query/allocation/concurrency/resource impact -> measurement or bounded reasoning`

If an artifact cannot be built because required context is missing, ask a concise decision question before editing, implement the smallest safe reversible step, or explicitly report the residual risk.

## Stop rules

Do not guess when the decision changes:
- externally visible product behavior;
- API/event/schema compatibility;
- data semantics or migration strategy;
- security posture, ownership, tenancy, or permissions;
- irreversible side effects;
- runtime ownership or deployment sequencing;
- architecture boundaries, dependency direction, or module ownership;
- performance/SLO tradeoff.

When blocked by one of these, ask a concise decision question or implement the smallest safe reversible step.

## Diff discipline

Keep the diff reviewable.

Before editing:
- identify files expected to change;
- avoid unrelated package moves, mass renames, formatting churn, dependency upgrades, generated-file churn, or architecture rewrites.

During editing:
- keep behavior, tests, config, migrations, and docs aligned;
- avoid mixing refactor and behavior change unless refactor is needed to make the behavior safe.

Before final response:
- self-review the diff for unrelated edits;
- remove accidental debug logs, temporary flags, dead code, unused dependencies, and broad formatting churn.

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
- What triggered surfaces and risk tier apply?
- What is the smallest safe vertical slice?

Ask the user only when the answer cannot be inferred from the repository or the decision changes product behavior, API contract, data semantics, security posture, performance expectations, or runtime ownership.

### 2. Plan the smallest safe vertical slice

For non-trivial work, form a short plan before editing:

- Desired behavior.
- Triggered surfaces and risk tier.
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

### 5. Run focused implementation passes

Run the corresponding focused pass for every triggered surface before finalizing the patch.

Each pass must affect at least one of:
- implementation plan;
- tests/verification;
- reference loading;
- self-review;
- final response.

Focused passes:
- Java/Spring pass -> framework semantics, transactions, validation, serialization, persistence behavior.
- Tests pass -> behavior proof, regression coverage, useful test level, flaky-risk control.
- API/contracts pass -> compatibility, request/response semantics, generated clients, errors, versioning.
- Data/migrations pass -> schema/data safety, transactions, rollout, rollback, data consistency.
- Security/privacy pass -> authz/authn, ownership, tenancy, secret/sensitive-data exposure.
- Runtime/resilience pass -> external calls, retries, timeouts, circuit breakers, rate limits, graceful degradation, idempotency, failure recovery.
- Observability/runtime-health pass -> logs, metrics, traces, health diagnostics, correlation continuity, cardinality, sensitive-data leakage.
- Kubernetes/container runtime pass -> container startup, manifests, probes, resources, env/config/secrets, ports, security context, deployment behavior.
- Architecture boundaries pass -> dependency direction, domain/application/infrastructure separation, use-case boundaries, refactoring scope.
- Performance/load pass -> query shape, hot path cost, allocation, concurrency, backpressure, SLO/cost risk.

Do not let a passing Java unit test suppress a triggered data, contract, security, runtime, observability, Kubernetes/container, architecture, or performance pass.

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

- correctness and intended behavior;
- triggered surfaces and risk tier;
- compatibility, tests, security, data, runtime/resilience, observability, Kubernetes/container, architecture, and performance only when triggered;
- refactoring scope and diff discipline;
- residual risks.

## Reference loading guide

Do not load every reference by default.

Load the corresponding reference for every non-trivial triggered surface:

- Java source quality -> `references/java-code-quality.md`.
- Spring framework behavior -> `references/spring-boot.md`.
- Tests and verification -> `references/testing.md`.
- API/contracts -> `references/microservices-contracts.md`.
- Data/migrations -> `references/data-transactions-migrations.md`.
- Runtime/resilience -> `references/resilience.md`.
- Observability/runtime-health -> `references/observability-runtime-health.md`.
- Security/privacy -> `references/security.md`.
- Architecture boundaries -> `references/clean-architecture-boundaries.md`.
- Kubernetes/container runtime -> `references/kubernetes-container-runtime.md`.
- Performance/load -> `references/performance-load.md`.

For trivial changes, a triggered reference may be skipped only when it would not affect the plan, implementation, verification, or final response. If skipped, briefly note why.

## Quality gates

Done means:

- Change is minimal, localized, and aligned with reasonable repository conventions.
- Touched code does not blindly preserve local bad practices when a low-risk improvement is possible.
- Public behavior/contracts remain backward-compatible or the migration path is explicit.
- Triggered focused passes were applied, with required references loaded or explicitly skipped.
- Tests/checks were run at the right level when available; compile/static checks are not used as behavior proof.
- Unverified risks, failed/skipped checks, flaky behavior, pre-existing issues, and follow-up refactoring opportunities are explicitly reported.

## Final response contract

Be concise and technical.

Final response must include:

- What changed.
- Why it changed.
- Surfaces touched.
- Tests/checks run with exact commands.
- What was not verified.
- Residual risks or follow-up refactoring opportunities, if any.

Do not explain generic best practices unless they directly justify a decision.
Do not hide failing checks, skipped checks, flaky behavior, or pre-existing issues.
