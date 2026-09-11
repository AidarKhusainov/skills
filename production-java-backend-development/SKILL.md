---
name: production-java-backend-development
description: Use when implementing, debugging, refactoring, testing, or technically evaluating review findings in production Java/Spring backend code where domain/framework semantics, persistence, contracts, security, distributed behavior, runtime, or performance are material. Dedicated PR/diff review is out of scope.
---

# Production Java Backend Development

This is a domain engineering skill for production Java/Spring backend work.

It defines backend constraints, decision gates, concern routing, and proof obligations. It does **not** own a generic software-development workflow. When a host workflow or process skill already governs brainstorming, planning, TDD sequencing, systematic debugging, worktrees, delegation, review reception, completion verification, or branch lifecycle, compose with that workflow instead of duplicating or contradicting it here.

It is application-first and platform-aware, but not a generic platform/IaC skill. For dedicated review-only analysis, use a specialist review skill such as `strict-backend-code-review` when that companion skill is available.

## Composition boundary

Apply this skill as the backend/domain layer inside the active task workflow.

- Explicit user/repository instructions and documented decisions govern within higher-priority constraints.
- If an explicit decision intentionally accepts a backend risk, preserve that decision and make the consequence explicit rather than silently overriding it.
- Inferred or repeated local conventions are evidence, not authority; do not copy an unsafe pattern merely for consistency.
- Generic workflow mechanics belong to the host or dedicated process skills.
- This skill owns Java/backend semantic constraints and the backend-specific evidence needed to evaluate or justify a change.
- Repository-local libraries, module rules, migration tooling, exact build commands, and intentional architecture/product trade-offs belong in `AGENTS.md` or equivalent project documentation.

If no dedicated process workflow is available, follow the repository's established workflow while still applying the domain constraints in this skill.

## Backend evaluation of review findings

When the task includes review feedback, evaluate backend-related findings as technical input to the active review-reception workflow. This skill does not decide whether feedback is sufficiently understood, when implementation may begin, whether clarification must happen first, or the order in which accepted fixes are applied.

For each backend-related finding:

1. Re-check the current repository and task context; the review may be stale or incomplete.
2. Independently classify the finding as `confirmed`, `rejected`, or `unresolved` against current code, tests, configuration, backend invariants, and relevant references.
3. For a rejected finding, provide the contradicting evidence. For an unresolved finding, state the missing evidence or ambiguity.
4. Preserve the original finding number/title when available so the active workflow can map the evaluation back to the review.
5. Treat unresolved questions or review limitations as confirmed defects only when additional evidence establishes a concrete problem.

Reviewer authority is never proof, and a review handoff never overrides current repository evidence. Return these backend evaluations to the active review-reception workflow; that workflow owns clarification gates, implementation readiness, sequencing, and response handling.

## Applying the backend domain layer

### Establish the affected semantics

Inspect only the repository context needed to determine what backend behavior and invariants are at risk. Depending on the change, that can include:

- relevant code paths and tests;
- API/event schemas and serialization;
- persistence mappings, queries, migrations, and transaction boundaries;
- configuration and service-owned runtime artifacts;
- ADRs, domain documentation, and module/bounded-context rules;
- security, tenancy, ownership, retry/idempotency, or performance constraints.

Prefer repository evidence over assumptions.

### Route material concerns

Identify the concerns materially affected by the change and load only their references from the routing table below.

A concern is material when its rules could change the implementation choice, compatibility boundary, required proof, rollout safety, or residual risk. Do not load references merely because a file type technically matches.

### Define backend-specific proof obligations

Use `references/testing.md` to select the narrowest proof that still exercises the semantics at risk.

The proof level is a backend/domain decision; the sequencing of test-first development or generic completion verification belongs to the active process workflow.

Examples:

- transaction, locking, dialect, constraint, or migration semantics may require a production-like database rather than repository mocks;
- Spring wiring, serialization, validation, transaction proxies, or lifecycle behavior may require a Spring slice or integration boundary;
- API/event compatibility must be checked against the actual externally visible representation and consumers' expectations;
- messaging, retries, duplicate delivery, idempotency, and ack/commit ordering require proof at the failure boundary they depend on;
- authorization, tenancy, and ownership changes require negative-path proof, not only happy-path access;
- performance-sensitive changes require measurement representative of the claimed latency, throughput, allocation, saturation, or cost property.

### Keep coupled backend artifacts aligned

When backend semantics change together, keep the relevant implementation, tests, schemas, migrations, configuration, runtime artifacts, and operational signals consistent with the same intended behavior.

Avoid unrelated cleanup, broad renames, dependency upgrades, generated-file churn, or architecture migration unless they are required to make the requested change safe and coherent.

## Decision gates

Do not invent a decision when neither current repository evidence nor explicit user/repository instructions determine:

- externally visible product behavior;
- an intentional breaking API/event/schema contract;
- destructive or irreversible data semantics/migration strategy;
- authorization, tenancy, ownership, or another security policy;
- irreversible external side effects or rollout sequencing with business impact;
- a required SLO, latency, throughput, or cost target that materially changes the solution.

If an explicit documented decision already resolves one of these trade-offs, preserve it and surface the backend consequence. Otherwise, return the unresolved decision to the active workflow rather than silently choosing product or operational policy.

Normal internal implementation choices such as class placement, local abstractions, test level, dependency direction, transaction implementation, or focused refactoring should be derived from repository evidence and the relevant domain references when the safe choice is clear.

## Reference routing

Load only references for material concerns:

- Java semantics, code structure, exceptions, mutability, concurrency, or abstraction cost -> `references/java-code-quality.md`.
- Spring wiring, configuration, controllers, serialization, transactions, clients, or framework lifecycle -> `references/spring-boot.md`.
- Backend proof level, regression coverage, framework/database integration, flaky-risk, or verification semantics -> `references/testing.md`.
- Externally consumed API/event/message/schema/error semantics or compatibility -> `references/microservices-contracts.md`.
- Persistence, queries, transactions, schema, migrations, indexes, constraints, backfills, or DB/event consistency -> `references/data-transactions-migrations.md`.
- External calls, retries, timeouts, idempotency, messaging failure, degradation, or cascading-failure risk -> `references/resilience.md`.
- Logs, metrics, traces, correlation, health semantics, diagnostics, startup/shutdown visibility -> `references/observability-runtime-health.md`.
- Authentication, authorization, tenancy, object ownership, sensitive data, secret handling, or trust boundaries -> `references/security.md`.
- Domain ownership, invariants, bounded contexts, use-case boundaries, dependency direction, or architectural refactoring -> `references/architecture-domain.md`.
- Service-owned Docker/Kubernetes/Helm/Kustomize behavior, probes, resources, security context, env/config, ports, or rollout mechanics -> `references/kubernetes-container-runtime.md`.
- Hot paths, large datasets, query/allocation cost, saturation, backpressure, JVM/resource pressure, SLO/cost-sensitive behavior, or performance measurement -> `references/performance-load.md`.

When concerns overlap, use the owning reference for the core rule and the adjacent reference only for its own perspective. For example, resilience owns retry safety; performance owns retry amplification under load. Observability owns application health semantics; Kubernetes owns how probes represent those semantics.

## Scope discipline

Improve touched code only when the improvement directly supports the requested change by reducing implementation risk, clarifying the changed behavior, or making it meaningfully testable. Do not turn a focused task into cleanup of surrounding legacy code.

Apply explicit user/repository instructions and documented architectural decisions before generic preferences. Treat inferred or repeated local patterns as evidence of convention, not authority; do not preserve accidental legacy patterns when a local, low-risk correction is necessary for the requested change.
