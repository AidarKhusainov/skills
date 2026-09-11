---
name: strict-backend-code-review
description: Use when performing dedicated review-only analysis of Java/Spring backend PRs or code diffs for correctness, contracts/data, security, runtime/concurrency, architecture/DDD, tests, or maintainability. Do not use for implementation or review-handoff validation.
metadata:
  version: "0.5.0"
---

# Strict Backend Code Review

This skill supplies specialist Java/Spring backend review analysis for PRs and code diffs. It is review-only and does not modify code.

It owns backend issue discovery, focused passes, evidence validation, false-positive challenge, negative-space analysis, and finding thresholds. The active host review workflow owns review orchestration and, when it defines them, output format, severity, recommendations, remediation fields, and merge verdict.

If fixes are also requested, complete the review analysis first; review reception, clarification, solution selection, and implementation belong to the active workflow. When the companion `production-java-backend-development` skill is available, it can provide the backend/domain layer for technical evaluation and remediation.

## Scope and authority

Within higher-priority constraints, explicit user/repository instructions and documented decisions define the review scope and accepted project boundaries. Do not broaden a deliberately scoped review merely because additional passes could apply.

If an explicit documented decision intentionally accepts a compatibility, security, data, availability, or runtime trade-off, preserve that decision and evaluate whether the implementation matches it; do not report the accepted trade-off itself as a defect merely because generic guidance would choose differently.

Treat inferred or repeated local conventions as evidence, not authority. A legacy pattern does not become correct merely because it is common in the repository.

## Host review contract

When an active host review workflow defines the review output, severity model, recommendation/remediation fields, or verdict, follow that contract. Apply this skill to determine **what backend issues survive review and what evidence supports them**, then map those results into the host-defined format.

Do not emit a second standalone handoff alongside a host-defined review report. Use the standalone output contract below only when no host review contract exists.

## Leading concepts

Changed surface:
Everything touched or implied by PR intent within the requested review scope: production code, tests, APIs, DTOs, schemas, events, domain rules, persistence, migrations, config, runtime artifacts, integrations, security, observability, and missing supporting changes.

Negative space:
Required code, tests, contracts, configs, manifests, migrations, docs, or operational artifacts that should have changed but did not.

Focused passes:
Domain-specific review passes that inspect their surface as first-class code, not as supporting context for Java.

Evidence artifacts:
Compact internal maps/tables created during focused passes to prove or disprove concrete risks. Do not print them by default.

Evidence gate:
No finding survives without concrete, checkable evidence tied to changed surface.

False-positive challenge:
Before reporting any finding, actively try to disprove it using existing code, tests, config, framework behavior, deployment setup, explicit project decisions, and repo-local convention.

Finding threshold:
Report a finding only when resolving it could materially change correctness, safety, contract behavior, architecture of the changed path, operability, testability, or maintainability relative to the requested behavior and accepted project decisions. Omit preferences and improvements that would not justify follow-up work.

Handoff boundary:
A backend finding is a technical conclusion supported by evidence. It does not itself authorize code changes; the active review-reception/implementation workflow owns what happens next.

## Review modes and execution

Default: Standard review.

Audit mode:
Use only when explicitly requested. Include compact focused-pass coverage in addition to the normal review result, without replacing any host-defined output contract.

Cover all focused passes triggered inside the requested scope. The active host workflow decides whether independent passes run sequentially or through delegation. If delegation is used, the coordinating reviewer must collect candidates, deduplicate by root cause, and re-apply the evidence gate, false-positive challenge, and finding threshold before producing the final result.

## Workflow

1. Read PR title, description, linked ticket, ADR/design note, acceptance criteria, rollout notes, and repo-local instructions when available.
2. Extract intent, requested scope, explicit project decisions, constraints, expected behavior, and risk surface.
3. Build changed surface inside that scope.
4. Classify changed files, diff hunks, and implied surfaces using the surface classifier.
5. Run every triggered focused pass and evaluate every triggered cross-cutting concern within the requested scope. Do not review a multi-domain PR as one flat diff.
6. Check negative space inside each triggered pass and cross-cutting concern.
7. Load references and playbooks required by triggered passes and cross-cutting concerns. Do not load unrelated references by default.
8. Inspect repository context only to prove or disprove concrete risks.
9. Audit changed behavior: map each review-relevant changed branch, guard, validator, invariant, false/failure path, observable behavior, and race/idempotency path to proof: test, finding, unresolved question, N/A, or review limitation.
10. Apply evidence gate and false-positive challenge to every candidate.
11. Apply the finding threshold.
12. Group surviving findings by root cause.
13. Return the result using the active host review contract; if none exists, use the standalone output contract below.

Completion criterion:
Every triggered focused pass and cross-cutting concern inside the requested scope is checked, not applicable, finding, unresolved question, or not fully reviewable.

Add a review limitation if missing context or foundational blockers make important verification unreliable.

## Surface classifier

Classify changed files, hunks, and implied surfaces only within the requested review scope.

A triggered pass in that scope must not be silently skipped, and a finding from one domain must not suppress another triggered domain pass. One changed file can trigger multiple passes.

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

When more than one domain is triggered, inspect each triggered domain independently.

Each pass must either:
- produce findings;
- produce unresolved questions;
- be explicitly checked with no finding in audit mode;
- or mark the review not fully reviewable if required evidence is unavailable.

A pass may produce no findings. It must still inspect its own changed surface as first-class code.

## Specialist stance

During each focused pass, review as the specialist accountable for that domain:

- Java/Spring pass: framework semantics, boundaries, transaction behavior, validation, persistence mapping, serialization, maintainability.
- Tests pass: changed behavior, regressions, failure modes, observable outcomes.
- DB/migrations pass: data integrity, constraints, indexes, locks, rollout, rollback, retention rules.
- OpenAPI/contracts pass: status semantics, compatibility, schema stability, validation, error shapes.
- Security/privacy pass: authz/authn, ownership, tenant isolation, secret/PII exposure, concrete attack paths.
- Runtime/concurrency pass: rollout safety, probes, shutdown, retries, timeouts, backpressure, duplicate delivery, idempotency, locking, races, diagnosability.

## Evidence artifacts

Build compact evidence artifacts internally when the corresponding pass is triggered:

- Java/Spring -> `entry point -> validation -> auth -> domain decision -> transaction -> persistence -> side effect -> response/error`
- Tests -> `changed behavior -> existing proof -> missing proof`
- DB/migrations -> schema-risk inventory, referential-constraint inventory, schema consistency table.
- OpenAPI/contracts -> operation matrix, contract-identifier and generator-impact scan.
- Security/privacy -> `caller -> identity -> permission/scope -> tenant/object ownership -> allowed action -> denied behavior -> tests`
- Runtime/concurrency -> `trigger -> read state -> lock/constraint -> write state -> side effect -> ack/commit -> retry result`

Do not print artifacts by default. Print compact artifacts only in audit mode or when needed to support a finding or unresolved question. If an artifact cannot be built because necessary context is unavailable, add a review limitation or turn the missing evidence into an unresolved question.

## Context budget

Do not inspect the entire repository by default.

Context priority:
1. PR intent artifacts and explicit project decisions.
2. Changed files and surrounding code.
3. Direct callers/callees, interfaces, tests, and configs.
4. Affected contracts, migrations, security rules, and runtime manifests.
5. Repo-local instructions for the touched module.
6. Broader search only for a concrete risk hypothesis.

Stop expanding context when the risk is verified, disproven, accepted by an explicit project decision, not applicable, or remaining uncertainty should become an unresolved question or review limitation.

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

## Scope gates

Report only issues introduced, exposed, depended on, implied, or materially amplified by the PR inside the requested review scope and relative to accepted project decisions.

Do not review unrelated legacy code. Apply Boy Scout cleanup only to touched or directly adjacent code when needed for understandability, testability, safety, or maintainability. Do not demand broad cleanup outside the changed area unless the PR cannot satisfy its requested behavior safely without it.

Do not report PR size, mixed scope, or lack of atomicity as a standalone finding. If mixed scope creates correctness, security, data, rollout, or architecture risk beyond an accepted project decision, report the concrete underlying risk.

Do not duplicate CI/linter/formatter/static-analysis/dependency-scanner output unless it reveals a non-obvious review root cause. Ignore mechanical style handled by tools.

Review naming, readability, and simplicity only when they affect domain intent, architecture, API semantics, security meaning, transaction/idempotency meaning, testability, or maintainability.

## Finding rules

Do not cap findings by count.

A published finding is an evidence-backed assertion, not a hypothesis or question. If evidence is insufficient, use `Unresolved questions` or `Review limitations` in the standalone contract, or the host workflow's corresponding uncertainty mechanism.

The standalone finding shape is:
1. Short factual title.
2. `Location` — repository-relative file/line plus symbol when available, or another precise inspectable surface such as config, migration, or contract. Avoid workstation-specific absolute paths.
3. `Problem` — what is wrong, why it matters, and enough concrete evidence for another agent to independently validate the claim.
4. `Expected` — the observable behavior, invariant, contract, or state that should hold when the problem is resolved.
5. `Evidence` — optional; use only when separating substantial proof improves clarity.
6. `Validation` — optional; use only when a specific observable check materially helps prove resolution.

When a host review contract defines different fields, map the same technical evidence into those fields instead of forcing the standalone shape.

Missing-test findings are valid for changed defensive contracts/invariants lacking behavioral proof when the proof gap itself crosses the finding threshold. Attach a test gap to the related finding when it is evidence for the same root cause.

## Expected, remediation, and validation

In the standalone handoff, `Expected` is outcome-oriented. Do not choose architecture, prescribe a patch, or provide suggested code there.

If the active host review contract requires remediation guidance or a suggested fix, provide it in the host-owned field after the finding has survived this skill's evidence gate and false-positive challenge.

`Validation` describes what observable behavior would prove resolution. Do not add it merely to fill the standalone schema.

## Standalone output fallback

Use this format only when no active host review contract defines another output.

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

Under this standalone fallback, do not output praise, positive notes, generic advice, process commentary, severity, recommendations, or a merge verdict. These fields may be required by an active host review contract and should be supplied there when requested.

## Audit mode output

When audit mode is requested, include a compact focused-pass coverage matrix in addition to the normal review result.

Coverage statuses:
- Checked;
- Not applicable;
- Finding;
- Unresolved question;
- Not fully reviewable.

For each triggered focused pass and cross-cutting concern, include trigger evidence, artifact built when applicable, status, and finding/unresolved-question references. Do not create duplicate findings from the matrix.

## Self-check

Before returning:
- no focused pass inside the requested scope was skipped because another domain already produced a finding;
- required evidence artifacts were built internally when DB/migrations, OpenAPI/contracts, security/privacy, or runtime/concurrency passes were triggered;
- every surviving finding is evidence-backed, precisely located, and materially relevant to the requested behavior and accepted project decisions;
- uncertainty is represented explicitly rather than promoted to a finding;
- no finding duplicates CI/linter/formatter output or reports unrelated legacy code;
- changed guards/invariants/failure/race paths are mapped to proof: test, finding, unresolved question, N/A, or review limitation;
- the final response follows the active host review contract, or the standalone fallback only when no host contract exists.
