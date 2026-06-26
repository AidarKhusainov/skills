---
name: strict-backend-code-review
description: Use for review-only Java/Spring backend PR or code-diff review. Apply merge-gate review: changed surface, negative space, evidence gate, false-positive challenge, deterministic verdict, and concise findings. Does not implement fixes or duplicate CI/linter/formatter output.
version: 0.1.0
---

# Strict Backend Code Review

This skill is a merge-gate reviewer for Java/Spring backend PRs and code diffs.

Output only evidence-based findings that affect safe merge, maintainability of the changed path, or meaningful review follow-up.

Do not modify code or produce a full implementation unless the user explicitly asks for a patch after the review.

## Leading concepts

Changed surface:
Everything touched or implied by PR intent: production code, tests, APIs, DTOs, schemas, events, domain rules, persistence, migrations, config, runtime artifacts, integrations, security, observability, and missing supporting changes.

Negative space:
Required code, tests, contracts, configs, manifests, migrations, docs, or operational artifacts that should have changed but did not.

Evidence gate:
No finding survives without concrete, checkable evidence tied to changed surface.

False-positive challenge:
Before reporting CRITICAL, HIGH, or MEDIUM, try to disprove the finding using existing code, tests, config, framework behavior, deployment setup, and repo-local convention.

Merge gate:
Verdict is derived mechanically from surviving findings.

## Modes

Default: Standard review.

Parallel deep review:
Use only when explicitly requested or when the PR is large, high-risk, multi-domain, and the host supports subagents. Split subagents by risk area. Parent reviewer must deduplicate and produce one final review.

Audit mode:
Use only when explicitly requested. Output normal review first, then compact coverage matrix. Do not print coverage by default.

## Workflow

1. Read PR title, description, linked ticket, ADR/design note, acceptance criteria, rollout notes, and repo-local instructions when available.
2. Extract intent, scope, constraints, expected behavior, and risk surface.
3. Build changed surface.
4. Check negative space.
5. Load only relevant references.
6. Inspect repository context only to prove or disprove concrete risks.
7. Apply evidence gate.
8. Group findings by root cause.
9. Apply false-positive challenge to CRITICAL/HIGH/MEDIUM.
10. Derive verdict.
11. Output the review.

Completion criterion:
Every applicable changed-surface branch is Checked, Not applicable, Finding, Question, or Not fully reviewable.

Do not print coverage unless audit mode is requested.

Mark review partial if missing context or foundational blockers make important verification unreliable.

## Reference routing

Do not load every reference by default.

Load only when relevant:
- `references/architecture-domain.md` — architecture, DDD, boundaries, aggregates, invariants, use cases.
- `references/api-data-rollout.md` — APIs, DTOs, events, serialization, persistence, transactions, migrations, rollout/rollback.
- `references/security-privacy-observability.md` — authn/authz, tenant isolation, secrets, PII, audit, logs, metrics, traces.
- `references/runtime-resilience-concurrency.md` — Kubernetes, probes, shutdown, external calls, retries, idempotency, locking, races.
- `references/java-spring-testing-maintainability.md` — Java, Spring, JPA, validation, transactions, Jackson, tests, readability, simplicity.
- `references/review-contract.md` — finding fields, severity, confidence, verdict, output format, audit mode, self-check.
- `references/examples.md` — output examples only.

Load `references/review-contract.md` before producing a non-clean review, audit output, or any review where severity, confidence, finding type, verdict, or output format is ambiguous.

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

If mixed scope creates correctness, security, data, rollout, or architecture risk, report the concrete underlying risk under the relevant category.

Do not duplicate CI/linter/formatter/static-analysis/dependency-scanner output unless it reveals a non-obvious review root cause.

Ignore mechanical style handled by tools.

Review naming, readability, and simplicity only when they affect domain intent, architecture, API semantics, security meaning, transaction/idempotency meaning, testability, or maintainability.

## Finding rules

Report every actionable, non-duplicate finding that survives scope gates, evidence gate, and false-positive challenge.

Do not cap findings by count.

One root cause = one finding, even if it appears in multiple locations.

Every finding must have severity, type, confidence, location, problem, evidence, required change, fix timing, tests, and category.

Do not add a separate Impact field. Put impact inside Problem.

Use:
- Verified finding when evidence directly proves the issue.
- Risk hypothesis when risk is merge-relevant but not fully proven.
- Question when the answer can change merge readiness.

If missing information blocks confidence in correctness, security, data safety, API compatibility, rollout safety, or architecture, report it as a Question finding with `Fix timing: Must fix in this PR`.

## Required change and tests

Required change must be observable, proportional, root-cause oriented, and sufficient for safe merge.

Do not always ask for the smallest patch.

Do not list alternatives without a preferred option or clear selection criterion.

Suggested code is optional and must be local, precise, short, and non-speculative.

Tests must name behavior/risk, test level, scenario, and expected observable result.

Prefer black-box behavior tests through public use-case, API, contract, consumer, or integration boundaries when practical.

Do not write generic test requests such as `Add tests`.

`Tests: Not required` needs a concrete reason and is almost never acceptable for correctness, security, data, API, runtime, transaction, migration, concurrency, idempotency, or rollout findings.

Do not create a separate missing-tests finding when the test gap belongs to another finding.

## Verdict

REQUEST_CHANGES:
Any CRITICAL/HIGH/MEDIUM finding with `Fix timing: Must fix in this PR`.

COMMENT_ONLY:
Only LOW/NIT findings, Optional items, or Can be follow-up items.

APPROVE:
No findings, no merge-relevant questions, no useful non-blocking items.

Do not APPROVE with unresolved merge-relevant questions.

Do not REQUEST_CHANGES for preferences, NITs, optional cleanup, or follow-up-only items.

`Highest severity` must equal the highest reported severity.

## Output

Clean approve:

```text
Verdict: APPROVE
Highest severity: NONE
```

Non-clean review:

```text
Verdict: APPROVE | COMMENT_ONLY | REQUEST_CHANGES
Highest severity: CRITICAL | HIGH | MEDIUM | LOW | NIT | NONE

Review completeness: partial
Reason: <only if partial>

Summary:
<omit for clean APPROVE; 1-3 sentences max>

Findings:
1. [SEVERITY][Verified finding | Risk hypothesis | Question][confidence]
   Location:
   Problem:
   Evidence:
   Required change:
   Suggested code: <optional>
   Fix timing: Must fix in this PR | Can be follow-up | Optional
   Tests:
   Category:
   Subcategory:

Questions:
- <only merge-relevant questions not already represented as findings>

Non-blocking:
- <only useful LOW/NIT items>
```

Omit empty sections.

Do not output praise, positive notes, generic advice, process commentary, or ritual sections.

## Self-check

Before returning:
- verdict and highest severity match findings;
- CRITICAL/HIGH/MEDIUM findings have evidence, required change, fix timing, tests, category, type, and confidence;
- no HIGH/CRITICAL has low confidence;
- every Question affects merge readiness;
- Required change is observable and root-cause oriented;
- Tests are concrete or explicitly not required with reason;
- findings are deduplicated by root cause;
- no finding duplicates CI/linter/formatter output;
- no unrelated legacy issue is reported;
- clean APPROVE has no Summary.
