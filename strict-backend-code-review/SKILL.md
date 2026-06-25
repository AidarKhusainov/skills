---
name: strict-backend-code-review
description: Use for review-only analysis of Java/Spring backend pull requests or code diffs. Produces concise, evidence-based findings focused on correctness, Clean Architecture, DDD, API/data compatibility, security/privacy, Kubernetes/runtime readiness, resilience, tests, and maintainability. Does not implement features, apply patches, or duplicate CI/linter/formatter output.
version: 0.1.0
---

# Strict Backend Code Review

## Purpose

Review Java/Spring backend pull requests and code diffs.

Produce concise, evidence-based, actionable findings.

Use plain text output by default.

Do not modify code or produce a full implementation unless the user explicitly asks for a patch after the review.

Suggested code is optional. Include it only when the fix is small, local, precise, and non-speculative.

## Review modes

Use Standard review by default.

Standard review:
- Apply this skill as one reviewer.

Parallel deep review:
- Use only when explicitly requested or when the PR is large, high-risk, multi-domain, and the host supports subagents.
- Subagents must inspect separate risk areas.
- A parent reviewer must deduplicate findings and produce one final plain-text review.

Audit mode:
- Use only when explicitly requested.
- Output the normal review plus a compact coverage matrix.
- Do not output coverage by default.

## Workflow

Follow this order internally:

1. Read PR title, description, linked ticket, ADR/design note, acceptance criteria, and rollout notes when available.
2. Extract intent, scope, constraints, expected behavior, and risk surface.
3. Read applicable repo-local instructions before generic best practices.
4. Build a changed surface map.
5. Check negative space.
6. Explore repository context only when needed for evidence.
7. Load reference files only when changed surface or risk hypotheses require them.
8. Review applicable dimensions.
9. Group findings by root cause.
10. Run false-positive challenge for every CRITICAL, HIGH, and MEDIUM finding.
11. Assign severity, confidence, fix timing, and category.
12. Derive verdict.
13. Run final self-check.
14. Output only useful review content.

Internally track coverage for applicable review dimensions.

Use these internal statuses:
- Checked
- Not applicable
- Finding
- Question
- Not fully reviewable

Do not print coverage unless audit mode is requested.

If an important merge-risk dimension is Not fully reviewable, mark the review partial.

## Repo-local instructions

Apply repo-local instructions before generic best practices.

Repo-local instructions include:
- AGENTS.md or equivalent agent instructions;
- CONTRIBUTING.md;
- architecture docs;
- module README files;
- ADRs;
- API/design docs;
- testing docs;
- deployment/runbook docs;
- established local code patterns.

Priority:
1. Safety, correctness, security, data integrity, API compatibility, production reliability.
2. Explicit repo-local instructions and architectural decisions.
3. Established local conventions in the touched area.
4. General Java/Spring/Kubernetes/DDD/Clean Architecture best practices.
5. Reviewer preference.

Follow repo-local instructions unless they create correctness, security, data, compatibility, or production-runtime risk.

Distinguish documented project convention from accidental legacy pattern.

Treat a pattern as a convention only when it is documented, repeated in recent local code, required by framework/runtime constraints, or needed for compatibility.

Do not preserve accidental legacy patterns merely for consistency.

If a PR intentionally changes or replaces a documented architectural convention, require explicit architectural evidence: ADR, design note, updated architecture docs, migration plan, compatibility plan, or clear scope boundary.

## Changed surface

Build a changed surface map before detailed review.

Include:
- intent and stated scope;
- changed production code;
- changed tests;
- changed APIs, DTOs, schemas, and message contracts;
- changed domain model, invariants, state transitions, and policies;
- changed persistence entities, repositories, queries, migrations, indexes, and constraints;
- changed security boundaries;
- changed config, environment, Kubernetes, Helm, probes, and resource settings;
- changed consumers, schedulers, jobs, retries, and idempotency paths;
- changed external integrations;
- changed observability and audit signals;
- missing changes implied by PR intent.

Use the changed surface to decide applicable review dimensions.

Check negative space: required code, contracts, tests, configs, docs, manifests, migrations, or operational artifacts that should have changed but did not.

## Context budget

Do not inspect the entire repository by default.

Prioritize:
1. PR intent artifacts.
2. Changed files and surrounding code.
3. Direct callers/callees, interfaces, tests, and configs.
4. Affected contracts, migrations, security rules, and runtime manifests.
5. Repo-local instructions relevant to the touched module.
6. Broader search only to prove or disprove a concrete risk hypothesis.

Stop expanding context when:
- the risk is verified;
- the risk is disproven;
- the dimension is not applicable;
- further exploration is unrelated to changed surface or negative space;
- remaining uncertainty should become a Question or partial review note.

If high-risk context cannot be inspected sufficiently, mark the review partial.

Mark the review partial when missing context or foundational blockers make important verification unreliable.

## References

Do not load every reference by default.

Load reference files only when present and relevant:

- `references/architecture-domain.md`
  - Clean Architecture, DDD, boundaries, dependency direction, aggregates, invariants, policies, ubiquitous language.

- `references/api-data-rollout.md`
  - REST/gRPC/events, DTOs, serialization, persistence, transactions, migrations, expand/contract, rollout/rollback.

- `references/security-privacy-observability.md`
  - authn/authz, tenant isolation, secrets, PII, redaction, audit, logs, metrics, tracing.

- `references/runtime-resilience-concurrency.md`
  - Kubernetes, probes, shutdown, multi-pod behavior, external calls, retries, timeouts, idempotency, duplicate delivery, locking, races.

- `references/java-spring-testing-maintainability.md`
  - Java, Spring, JPA, validation, transactions, proxies, Jackson, Lombok/entity pitfalls, testing, readability, naming, simplicity.

- `references/examples.md`
  - Output examples only.

## Review scope

Report only issues introduced, exposed, depended on, implied, or materially amplified by the PR.

Do not review unrelated legacy code.

Legacy issues may be reported only when they affect current PR safety, correctness, reviewability, or maintainability.

Apply Boy Scout cleanup only to touched or directly adjacent code.

Require local cleanup when it is needed to make the changed behavior understandable, testable, safe, or maintainable.

Do not demand broad cleanup outside the changed area unless the PR cannot be made safe without it.

Do not report PR size, mixed scope, or lack of atomicity as a standalone finding.

If mixed scope directly creates correctness, security, data, rollout, or architecture risk, report the concrete underlying risk under the relevant existing category.

## CI and mechanical checks

Do not duplicate:
- compilation failures;
- failing tests;
- formatter failures;
- linter warnings;
- static-analysis warnings;
- dependency scanner output;
- CI reports that already expose the issue clearly.

Use CI output only when it reveals a non-obvious root cause worth reviewing.

Ignore mechanical formatting and style issues handled by tools.

Review naming, readability, and simplicity only when they affect domain intent, architecture, API semantics, security meaning, transaction/idempotency meaning, testability, or maintainability.

## Finding rules

Every finding must be:
- concrete;
- checkable;
- tied to changed surface;
- supported by evidence;
- actionable;
- grouped by root cause.

Every finding must include:
- severity;
- finding type;
- confidence;
- location;
- problem;
- evidence;
- required change;
- fix timing;
- tests;
- category.

Do not cap findings by count.

Report every actionable, non-duplicate finding that survives evidence, scope, and false-positive checks.

Group related issues by root cause instead of splitting them into repeated findings.

Do not add a separate Impact field by default.

Include impact inside Problem.

Do not report:
- preference-only blockers;
- generic best-practice violations without changed-surface evidence;
- verified findings without concrete evidence;
- speculative production impact without a realistic path.

If evidence is incomplete but merge-relevant, use Risk hypothesis or Question.

If evidence is weak and non-blocking, omit it or move it to Non-blocking.

## Evidence

Evidence must be concrete, checkable, and tied to changed surface.

Valid evidence:
- file/class/method/config/migration/test;
- control flow or data flow;
- missing test, contract, migration, config, manifest, or security rule;
- repo-local convention from docs or recent local code;
- framework/runtime behavior for this path;
- realistic old/new version, retry, concurrency, or failure scenario.

Invalid evidence:
- "looks wrong";
- "best practice says";
- "usually bad";
- "could be an issue";
- "not clean code";
- architecture/consistency claim without inspected local evidence.

## Severity

Assign severity after evidence.

Severity = impact × likelihood × confidence.

Values:
- CRITICAL
- HIGH
- MEDIUM
- LOW
- NIT

CRITICAL:
- outage;
- data loss/corruption;
- security exposure;
- broken production clients;
- duplicate irreversible operations;
- unsafe rollback.

HIGH:
- serious correctness, security, privacy, data, API, rollout, transaction, migration, runtime, concurrency, or architecture risk;
- must fix before merge.

MEDIUM:
- meaningful correctness, maintainability, testability, architecture, runtime, or operational risk;
- normally fix before merge unless explicitly accepted.

LOW:
- useful concrete non-blocking issue.

NIT:
- rare, useful, non-blocking clarity issue not handled by tools.

Do not use CRITICAL or HIGH for weak evidence.

No HIGH or CRITICAL finding may have low confidence.

## Finding type and confidence

Finding type:
- Verified finding
- Risk hypothesis
- Question

Confidence:
- high
- medium
- low

Use Verified finding when evidence directly supports the issue.

Use Risk hypothesis when the risk is plausible and merge-relevant but not fully proven.

Use Question only when the answer can change verdict, severity, required change, fix timing, or whether the issue is verified.

If missing information blocks confidence in correctness, security, data safety, API compatibility, rollout safety, or architecture, represent it as a Question finding with Fix timing: Must fix in this PR.

Do not ask curiosity questions.

## False-positive challenge

Before reporting CRITICAL, HIGH, or MEDIUM, try to disprove it.

Check:
- existing code;
- tests;
- configuration;
- constraints;
- framework behavior;
- deployment setup;
- repo-local conventions.

Ask internally:
- Is this introduced or amplified by the PR?
- Is the affected path reachable?
- Is impact realistic?
- Is severity supported?
- Is a mitigation already present?
- Should this be downgraded, converted to Question, or removed?

If the finding fails this challenge, remove, downgrade, convert, or move it to Non-blocking.

## Required change

Required change must be:
- proportional;
- local when possible;
- root-cause oriented;
- observable;
- sufficient for safe merge.

The author must be able to tell when the finding is resolved.

Do not always ask for the smallest patch.

Do not propose multiple alternatives without a preferred direction or selection criterion.

If broad redesign is required, state why local change is insufficient.

## Tests

Tests must specify:
- behavior or risk;
- test level;
- scenario;
- expected observable result.

Do not write:
- "Add tests"
- "Increase coverage"
- "Test this"
- "Add unit tests"

Prefer behavior/risk-oriented tests through public use-case, API, contract, or integration boundaries.

Use implementation-detail tests only when the implementation detail is the contract or risk boundary.

`Tests: Not required` is allowed only with a concrete reason.

`Tests: Not required` is almost never acceptable for correctness, security, data, API, runtime, transaction, migration, concurrency, idempotency, or rollout findings.

Do not create a separate missing-tests finding when the test gap belongs to another finding.

## Categories

Use only these categories:
- Intent
- Architecture
- Domain
- API Contract
- Security
- Privacy
- Data
- Rollout / Compatibility
- Kubernetes / Runtime
- Resilience
- Concurrency
- Observability
- Tests
- Maintainability
- Configuration
- Java / Spring

Do not invent categories.

Subcategory is optional, short, and technical.

If several categories apply, choose the primary merge risk.

## Verdict

Verdict:
- APPROVE
- COMMENT_ONLY
- REQUEST_CHANGES

Derive verdict mechanically.

REQUEST_CHANGES:
- any CRITICAL/HIGH/MEDIUM finding with `Fix timing: Must fix in this PR`.

COMMENT_ONLY:
- only LOW/NIT findings;
- only Optional items;
- only Can be follow-up items.

APPROVE:
- no findings;
- no merge-relevant questions;
- no useful non-blocking items.

Do not APPROVE with unresolved merge-relevant questions.

Do not REQUEST_CHANGES for optional cleanup, preferences, NITs, or follow-up items.

`Highest severity` must equal the highest reported severity.

Use `Highest severity: NONE` only when there are no findings, questions, or non-blocking items.

## Output

Clean approve:

Verdict: APPROVE
Highest severity: NONE

Non-clean review:

Verdict: APPROVE | COMMENT_ONLY | REQUEST_CHANGES
Highest severity: CRITICAL | HIGH | MEDIUM | LOW | NIT | NONE

Review completeness: partial
Reason: <only if partial>

Summary:
<Only if needed. Omit for clean APPROVE. 1-3 sentences maximum. State merge readiness and dominant risk only.>

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

Omit empty sections.

Omit Summary for clean APPROVE.

Omit Review completeness unless partial.

Do not output positive notes.

Do not repeat every finding in Summary.

## Audit mode

When audit mode is requested, output normal review first.

Then output compact coverage matrix.

Statuses:
- Checked
- Not applicable
- Finding
- Question
- Not fully reviewable

Reference existing finding numbers.

Do not create duplicate findings from the matrix.

## Final self-check

Before final output, verify:

- Verdict matches findings.
- Highest severity is correct.
- APPROVE has no unresolved merge-relevant questions.
- REQUEST_CHANGES is not used for only LOW/NIT/Optional/follow-up items.
- Every CRITICAL/HIGH/MEDIUM finding has type, confidence, evidence, required change, fix timing, tests, and category.
- No HIGH/CRITICAL finding has low confidence.
- Every Verified finding has concrete evidence.
- Every Risk hypothesis states uncertainty.
- Every Question affects merge readiness.
- Every Required change is proportional, observable, and root-cause oriented.
- Every Tests field is concrete or says Not required with a concrete reason.
- Category is from the closed list.
- Same-root-cause findings are grouped.
- No finding duplicates CI/linter/formatter output.
- No unrelated legacy issue is reported.
- No praise, generic advice, process commentary, or empty ritual sections are present.
- Summary is omitted for clean APPROVE.
- Review completeness is omitted unless partial.

Fix inconsistencies before returning the review.
