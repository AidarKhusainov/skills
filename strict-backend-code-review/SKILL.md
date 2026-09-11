---
name: strict-backend-code-review
description: Use for dedicated review-only discovery of concrete problems in Java/Spring backend PRs or code diffs, especially before merge or task completion. Covers correctness, architecture/DDD, contracts/data, security/privacy, runtime/resilience/concurrency, tests, and maintainability. Do not use to validate an existing review handoff or implement fixes; use `production-java-backend-runtime` for those tasks.
metadata:
  version: "0.3.0"
---

# Strict Backend Code Review

This skill reviews Java/Spring backend PRs and code diffs for concrete problems in the changed path.

The output is a factual handoff for independent validation, not an implementation plan.

Do not modify code. If the user also requests fixes, complete the review handoff first; validation, solution selection, and implementation belong to `production-java-backend-runtime`.

## Leading concepts

Changed surface:
Everything touched or implied by PR intent: production code, tests, APIs, DTOs, schemas, events, domain rules, persistence, migrations, config, runtime artifacts, integrations, security, observability, and missing supporting changes.

Negative space:
Required code, tests, contracts, configs, manifests, migrations, docs, or operational artifacts that should have changed but did not.

Focused passes:
Triggered domain-specific review passes that inspect their surface as first-class code, not as supporting context for Java.

Evidence artifacts:
Compact internal maps/tables created during focused passes to prove or disprove concrete risks. Do not print them by default.

Evidence gate:
No finding survives without concrete, checkable evidence tied to changed surface.

False-positive challenge:
Before reporting any finding, actively try to disprove it using existing code, tests, config, framework behavior, deployment setup, and repo-local convention.

Finding threshold:
Report a finding only when resolving it could materially change correctness, safety, contract behavior, architecture of the changed path, operability, testability, or maintainability. Omit preferences and improvements that would not justify follow-up work.

Handoff boundary:
A finding describes a problem and the observable state that should hold when it is resolved. It does not authorize code changes. The receiving agent independently validates the finding before solution analysis.

## Modes

Default: Standard review.

Parallel deep review:
Use only when explicitly requested or when the PR is large, high-risk, multi-domain, and the host supports subagents or deep research. Split subagents by triggered focused pass. Parent reviewer must collect candidates, deduplicate by root cause, re-apply the evidence gate, false-positive challenge, and finding threshold, and produce one final review.

If subagents are unavailable, run the same focused passes sequentially in the main reviewer.

Audit mode:
Use only when explicitly requested. Output normal review first, then compact focused-pass coverage matrix. Do not print coverage by default.

## Workflow

1. Read PR title, description, linked ticket, ADR/design note, acceptance criteria, rollout notes, and repo-local instructions when available.
2. Extract intent, scope, constraints, expected behavior, and risk surface.
3. Build changed surface.
4. Classify changed files, diff hunks, and implied surfaces using the surface classifier.
5. Run every triggered focused pass and evaluate every triggered cross-cutting concern. Do not review a multi-domain PR as one flat diff.
6. Check negative space inside each triggered pass and cross-cutting concern.
7. Load references and playbooks required by triggered passes and cross-cutting concerns. Do not load unrelated references by default.
8. Inspect repository context only to prove or disprove concrete risks.
9. Audit changed behavior: map each review-relevant changed branch, guard, validator, invariant, false/failure path, observable behavior, and race/idempotency path to proof: test, finding, unresolved question, N/A, or review limitation.
10. Apply evidence gate and false-positive challenge to every candidate.
11. Apply the finding threshold.
12. Group surviving findings by root cause.
13. Output the review as a compact handoff.

Completion criterion:
Every triggered focused pass and cross-cutting concern is checked, not applicable, finding, unresolved question, or not fully reviewable.

Add a review limitation if missing context or foundational blockers make important verification unreliable.

## Surface classifier

Before detailed review, classify changed files, hunks, and implied surfaces.

A triggered pass is mandatory. Do not silently skip it, and do not let a finding from one domain suppress another domain pass.

One changed file can trigger multiple passes. Review each triggered domain separately.

- Java/Spring pass:
  `src/main/java/**`, Spring controllers/services/configuration, validation, Jackson, transactions, JPA entities/repositories, or changed Java framework semantics.
- Tests pass:
  `src/test/**`, test fixtures, contract tests, integration tests, mocks/stubs, or changed risky behavior without matching proof.
- DB/migrations pass:
  `*.sql`, `db/**`, `migration/**`, `migrations/**`, Liquibase/Flyway, `CREATE TABLE`, `ALTER TABLE`, `ADD CONSTRAINT`, `CREATE INDEX`, `DROP INDEX`, or changed persistence schema.
- OpenAPI/contracts pass:
  `openapi*.yaml`, `openapi/**/*.yaml`, `*.proto`, generated API interfaces, public DTOs, `operationId`, `responses`, `$ref`, `required`, `components.schemas`, or externally consumed contract changes.
- Security/privacy pass:
  authentication, authorization, tenant/user/object ownership, scopes/roles/permissions, secrets, tokens, PII, audit-sensitive operations, or security-sensitive domain actions.
- Runtime/concurrency pass:
  Kubernetes/Helm/manifests, config/env, probes, resources, external calls, retries, timeouts, circuit breakers, async jobs, consumers, schedulers, locks, idempotency, duplicate delivery, or race-prone state transitions.

Architecture/DDD and observability are cross-cutting concerns, not separate focused passes. Evaluate them within affected passes, or directly when the concern is the only affected surface, whenever changed or implied surface touches architecture/domain ownership or boundaries, or logs/metrics/traces/audit/diagnostics/health/failure visibility.

## Focused passes

When more than one domain is triggered, do not review the PR as one flat diff.

Run each triggered focused pass independently.

Each pass must either:
- produce findings;
- produce unresolved questions;
- be explicitly checked with no finding in audit mode;
- or mark the review not fully reviewable if required evidence is unavailable.

A pass may produce no findings. It must still inspect its own changed surface as first-class code.

## Specialist stance

During each focused pass, review as the specialist accountable for that domain:

- Java/Spring pass: senior Spring backend reviewer responsible for framework semantics, boundaries, transaction behavior, validation, persistence mapping, serialization, and maintainability.
- Tests pass: test-design reviewer responsible for proving changed behavior, regressions, failure modes, and observable outcomes.
- DB/migrations pass: database schema and migration safety reviewer responsible for data integrity, constraints, indexes, locks, rollout, rollback, and retention rules.
- OpenAPI/contracts pass: external contract and generated-client reviewer responsible for status semantics, compatibility, schema stability, validation, and error shapes.
- Security/privacy pass: application security reviewer responsible for authz/authn, ownership, tenant isolation, secret/PII exposure, and concrete attack paths.
- Runtime/concurrency pass: SRE and distributed-systems reviewer responsible for rollout safety, probes, shutdown, retries, timeouts, backpressure, duplicate delivery, idempotency, locking, races, and diagnosability.

## Evidence artifacts

Build compact evidence artifacts internally when the corresponding pass is triggered.

Required artifacts:
- Java/Spring -> behavior map:
  `entry point -> validation -> auth -> domain decision -> transaction -> persistence -> side effect -> response/error`
- Tests -> proof map:
  `changed behavior -> existing proof -> missing proof`
- DB/migrations -> schema-risk inventory, referential-constraint inventory, schema consistency table.
- OpenAPI/contracts -> operation matrix, contract-identifier and generator-impact scan.
- Security/privacy -> access-control trace:
  `caller -> identity -> permission/scope -> tenant/object ownership -> allowed action -> denied behavior -> tests`
- Runtime/concurrency -> state-transition view:
  `trigger -> read state -> lock/constraint -> write state -> side effect -> ack/commit -> retry result`

Do not print artifacts by default. Use them as internal evidence. Print compact artifacts only in audit mode or when needed to support a finding or unresolved question.

If an artifact cannot be built because necessary context is unavailable, add a review limitation or turn the missing evidence into an unresolved question.

## Context budget

Do not inspect the entire repository by default.

Context priority:
1. PR intent artifacts.
2. Changed files and surrounding code.
3. Direct callers/callees, interfaces, tests, and configs.
4. Affected contracts, migrations, security rules, and runtime manifests.
5. Repo-local instructions for the touched module.
6. Broader search only for a concrete risk hypothesis.

Stop expanding context when the risk is verified, disproven, not applicable, or remaining uncertainty should become an unresolved question or review limitation.

## Reference routing

Do not load every reference by default.

Load the corresponding reference for every triggered pass or cross-cutting concern:
- Architecture/DDD concern -> `references/architecture-domain.md`.
- Java/Spring or Tests pass -> `references/java-spring-testing-maintainability.md`.
- DB/migrations pass -> `references/db-migrations-playbook.md` and `references/api-data-rollout.md`.
- OpenAPI/contracts pass -> `references/openapi-contract-playbook.md` and `references/api-data-rollout.md`.
- Security/privacy pass -> `references/security-privacy-observability.md`.
- Observability concern -> `references/security-privacy-observability.md`.
- Runtime/concurrency pass -> `references/runtime-resilience-concurrency.md`.
- API/data/serialization/events/transactions/rollout compatibility risk -> `references/api-data-rollout.md`.
- Finding-vs-uncertainty, Problem/Evidence separation, Expected boundary, missing-test ambiguity, or constrained no-finding output -> `references/review-contract.md`.
- Output examples only -> `references/examples.md`.

## Repo-local priority

Apply repo-local instructions before generic best practices.

Priority:
1. Safety, correctness, security, data integrity, API compatibility, production reliability.
2. Explicit repo-local instructions and architectural decisions.
3. Established local conventions in the touched area.
4. General Java/Spring/Kubernetes/DDD/Clean Architecture best practices.
5. Reviewer preference.

Follow repo-local instructions unless they create correctness, security, data, compatibility, or production-runtime risk.

Treat a pattern as project convention only when documented, repeated in recent local code, required by framework/runtime constraints, or needed for compatibility.

Do not preserve accidental legacy patterns merely for consistency.

If the PR changes or replaces a documented architectural convention, require ADR, design note, updated architecture docs, migration plan, compatibility plan, or clear scope boundary.

## Scope gates

Report only issues introduced, exposed, depended on, implied, or materially amplified by the PR.

Do not review unrelated legacy code.

Apply Boy Scout cleanup only to touched or directly adjacent code when needed for understandability, testability, safety, or maintainability.

Do not demand broad cleanup outside the changed area unless the PR cannot be made safe without it.

Do not report PR size, mixed scope, or lack of atomicity as a standalone finding.

If mixed scope creates correctness, security, data, rollout, or architecture risk, report the concrete underlying risk.

Do not duplicate CI/linter/formatter/static-analysis/dependency-scanner output unless it reveals a non-obvious review root cause.

Ignore mechanical style handled by tools.

Review naming, readability, and simplicity only when they affect domain intent, architecture, API semantics, security meaning, transaction/idempotency meaning, testability, or maintainability.

## Finding rules

Do not cap findings by count.

A published finding is an evidence-backed assertion, not a hypothesis or question. If evidence is insufficient, use `Unresolved questions` or `Review limitations`.

Each finding contains, in order:
1. Short factual title.
2. `Location` — repository-relative file/line plus symbol when available, or another precise inspectable surface such as config, migration, or contract. Avoid workstation-specific absolute paths.
3. `Problem` — what is wrong, why it matters, and enough concrete evidence for another agent to independently validate the claim.
4. `Expected` — the observable behavior, invariant, contract, or state that should hold when the problem is resolved.
5. `Evidence` — optional; use only when separating substantial proof improves clarity.
6. `Validation` — optional; use only when a specific observable check materially helps prove resolution.

Missing-test findings are valid for changed defensive contracts/invariants lacking behavioral proof when the proof gap itself crosses the finding threshold. Attach a test gap to the related finding when it is evidence for the same root cause.

## Expected and validation

`Expected` is outcome-oriented. Do not choose architecture, prescribe a patch, or provide suggested code. Leave solution analysis to the receiving agent.

`Validation` describes what observable behavior would prove resolution. Do not add it merely to fill the schema.

## Output

Complete review with no findings, unresolved questions, or review limitations:

```text
No findings.
```

Review with findings:

```text
Findings:

1. <short factual title>
   Location:
   Problem:
   Expected:
   Evidence: <optional>
   Validation: <optional>

Unresolved questions:
- <only questions whose answer could materially change the review>

Review limitations:
- <only unavailable context or verification that materially limits the review>
```

Omit optional fields and empty sections.

If no finding survives but unresolved questions or review limitations remain, output those sections and end with `No findings in the reviewed scope.`

Do not output praise, positive notes, generic advice, process commentary, or a summary that restates the findings.

## Audit mode output

When audit mode is requested, output normal review first, then compact focused-pass coverage matrix.

Coverage statuses:
- Checked;
- Not applicable;
- Finding;
- Unresolved question;
- Not fully reviewable.

For each triggered focused pass and cross-cutting concern, include:
- trigger evidence;
- artifact built when applicable;
- status;
- finding/unresolved-question references.

Reference existing finding numbers.

Do not create duplicate findings from the matrix.

## Self-check

Before returning:
- no focused pass was skipped because another domain already produced a finding;
- required evidence artifacts were built internally when DB/migrations, OpenAPI/contracts, security/privacy, or runtime/concurrency passes were triggered;
- every finding has a factual title, precise location, self-contained problem, and observable expected state;
- optional Evidence or Validation adds information rather than repeating another field;
- uncertainty is reported as an unresolved question or review limitation, not as a finding;
- no finding duplicates CI/linter/formatter output or reports unrelated legacy code;
- changed guards/invariants/failure/race paths are mapped to proof: test, finding, unresolved question, N/A, or review limitation;
- output follows the compact handoff contract.