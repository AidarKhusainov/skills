---
name: production-java-backend-development
description: Use for implementing, debugging, testing, refactoring, self-review of implementation work, or validating review handoffs in production Java backend services. Covers Spring Boot, APIs/events, persistence/migrations, distributed failures, observability, security, architecture/domain boundaries, performance-sensitive paths, and service-owned container/Kubernetes changes. For dedicated review-only analysis of existing code, PRs, or diffs, use `strict-backend-code-review` instead.
---

# Production Java Backend Development

Use this skill to implement production Java backend changes safely and with bounded scope.

It is application-first and platform-aware, but not a generic platform/IaC skill. For dedicated review-only analysis, use `strict-backend-code-review`.

## Task authority

Within applicable higher-priority constraints, explicit user instructions define the requested scope and authority; generic guidance in this skill does not expand or narrow an explicit request.

- Requests whose goal is analysis or verification—such as explain, review, diagnose, debug, inspect, test, verify, run, compile, or plan—are read-only unless the user also asks for repository changes.
- Requests whose goal is to modify the repository—such as implement, change, fix, refactor, update, add, remove, or migrate—authorize in-scope reversible edits and relevant non-destructive verification without another approval.
- When implementation is already requested, continue through implementation and verification rather than stopping after a plan or offering to continue.
- Ask only when essential information or access is missing, or when an unresolved choice crosses a decision gate below.

## Review handoff

When the task starts from a handoff produced by `strict-backend-code-review`:

1. Re-check the current repository and task context; the handoff may be stale.
2. Independently classify each finding as `confirmed`, `rejected`, or `unresolved`. Preserve the original finding number/title when reporting validation.
3. For rejected findings, cite the contradicting evidence. For unresolved findings, state the missing evidence. Do not fix either state.
4. Investigate unresolved questions and review limitations when the missing context is available. A gap becomes a confirmed issue only when evidence establishes a concrete defect.
5. If no confirmed handoff issue remains, no remediation is required for the handoff. Continue with any independently requested work; otherwise report the validation result and stop without code changes.
6. If the user asked only for validation, report confirmed/rejected/unresolved states with relevant evidence or remaining gaps and stop.
7. When the user requested a recommendation or implementation, identify the root cause of each confirmed issue and choose the smallest sound solution. Present alternatives only when materially different viable approaches exist.
8. If the user requested fixes or implementation, validation is mandatory but does not require a second approval; continue with confirmed issues unless a decision gate below is reached. If the user requested only a recommendation, stop before editing.

Reviewer authority is never proof, and a handoff never overrides current repository evidence.

## Workflow

### 1. Understand before editing

Inspect the smallest repository context needed to understand the requested behavior and existing constraints:

- relevant code path and tests;
- repo-local instructions such as `AGENTS.md`;
- build files and verification commands;
- affected API/event schemas, migrations, configuration, or runtime artifacts;
- ADRs or domain documentation when the change touches an established boundary.

Prefer repository evidence over asking the user.

### 2. Route material concerns

Identify the concerns materially affected by the change and load only their references from the routing table below.

A concern is material when its rules could change the implementation, verification, or residual risk. Do not load references merely because a file type technically matches.

### 3. Plan the smallest safe change

For non-trivial work, form a short implementation plan around:

- intended observable behavior;
- smallest coherent vertical slice;
- files or modules expected to change;
- narrowest useful feedback loop;
- materially affected concerns and compatibility/runtime risks.

Do not create architecture ceremony or speculative abstractions. Follow reasonable repository conventions, but do not copy an unsafe local pattern merely for consistency.

### 4. Implement feedback-loop-first

For behavior changes and bug fixes, prefer this order when practical:

1. Reproduce or define the expected behavior.
2. Add or update the narrowest useful behavioral proof.
3. Make the smallest implementation change.
4. Run the narrow check.
5. Refactor only what is needed to keep the changed path clear, safe, and testable.

For changes that cannot reasonably be test-first, establish another executable feedback loop before broad editing.

Keep behavior, tests, schemas, migrations, configuration, and runtime artifacts aligned when they change together. Avoid unrelated cleanup, package moves, mass renames, dependency upgrades, generated-file churn, or broad formatting changes.

### 5. Verify proportionally to risk

Discover commands from repository instructions, wrappers, build files, CI, and local docs. Prefer `./gradlew` over `gradle` and `./mvnw` over `mvn` when available.

Verify narrow-first, then broaden only as justified:

1. focused behavior or regression check;
2. compile/static check for the touched module when useful;
3. relevant integration/contract/migration/runtime/performance check;
4. broader module or repository checks when the change warrants them.

Compilation is not proof of behavioral, contract, data, security, distributed-runtime, or performance semantics.

Never claim a check passed unless it ran and passed. Distinguish failures caused by the change from evidenced pre-existing failures.

### 6. Self-review the final diff

Before finishing:

- confirm the requested behavior is implemented and no unrelated behavior changed;
- inspect the final diff for accidental churn, debug code, temporary flags, dead code, or unused dependencies;
- re-check every material concern using its loaded reference;
- confirm tests and verification match the actual risk;
- identify material residual uncertainty rather than hiding it.

## Decision gates

Do not guess when repository/task evidence does not determine a decision about:

- externally visible product behavior;
- an intentional breaking API/event/schema contract;
- destructive or irreversible data semantics/migration strategy;
- authorization, tenancy, ownership, or another security policy;
- irreversible external side effects or rollout sequencing with business impact;
- a required SLO, latency, throughput, or cost target that materially changes the solution.

Ask one concise decision question when such a choice is genuinely unresolved.

Do not escalate normal implementation choices such as class placement, internal abstractions, test level, dependency direction, transaction implementation, or local refactoring when repository evidence is sufficient to choose them safely.

## Reference routing

Load only references for material concerns:

- Java semantics, code structure, exceptions, mutability, concurrency, or abstraction cost -> `references/java-code-quality.md`.
- Spring wiring, configuration, controllers, serialization, transactions, clients, or framework lifecycle -> `references/spring-boot.md`.
- Behavioral proof, regression coverage, test level, flaky-risk, or verification strategy -> `references/testing.md`.
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

Apply explicit repo-local instructions and architectural decisions before generic guidance unless they create correctness, security, data-integrity, compatibility, or production-runtime risk. Treat documented and repeated recent patterns as evidence of convention; do not preserve accidental legacy patterns when a local, low-risk correction is necessary for the requested change.

## Final response

After implementation, report concisely:

- what changed and why;
- checks actually run, including exact commands when available;
- material behavior that could not be verified;
- material residual risks or follow-up work, only when they exist.

Do not report internal routing, loaded references, or procedural bookkeeping unless it explains a real limitation.
