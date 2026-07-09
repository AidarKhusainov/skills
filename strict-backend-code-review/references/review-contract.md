# Review Contract Reference

Load this file before producing a non-clean review, audit output, or any review where severity, confidence, finding type, verdict, or output format is ambiguous.

`SKILL.md` is the source of truth for the core workflow. This file defines detailed field semantics and output edge cases.

## Finding fields

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

Optional fields:
- suggested code;
- subcategory.

Do not add a separate Impact field by default. Put impact in Problem.

## Finding type

Use only:
- Verified finding;
- Risk hypothesis;
- Question.

Verified finding:
- evidence directly supports the issue;
- affected path is reachable or realistically implied by changed surface;
- mitigation is not already present.

Risk hypothesis:
- risk is plausible and merge-relevant;
- evidence is incomplete;
- missing evidence is stated explicitly.

Question:
- answer can change verdict, severity, required change, fix timing, or verification status;
- not a curiosity question.

If missing information blocks confidence in correctness, security, data safety, API compatibility, rollout safety, or architecture, represent it as a Question finding with `Fix timing: Must fix in this PR`.

## Confidence

Use only:
- high;
- medium;
- low.

High:
- evidence is direct;
- local mitigation was checked or is not applicable;
- impact path is realistic.

Medium:
- evidence is strong enough for review action;
- some context is missing or assumptions remain;
- risk is still merge-relevant.

Low:
- use only for non-blocking items or questions;
- do not use for HIGH or CRITICAL findings.

## Severity

Severity values:
- CRITICAL;
- HIGH;
- MEDIUM;
- LOW;
- NIT.

Assign severity after evidence.

Severity must follow impact, likelihood, and confidence.

CRITICAL:
- outage;
- data loss or corruption;
- security exposure;
- broken production clients;
- duplicate irreversible operations;
- unsafe rollback.

HIGH:
- serious correctness, security, privacy, data, API compatibility, rollout, transaction, migration, runtime, concurrency, or architecture risk;
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

## Evidence

Evidence must be concrete, checkable, and tied to changed surface.

Valid evidence includes:
- file/class/method/config/migration/test;
- observed control flow or data flow;
- missing required test, contract, migration, config, manifest, or security rule;
- repo-local convention from docs or recent local code;
- applicable framework/runtime behavior;
- realistic old/new version, retry, concurrency, rollout, or failure scenario;
- focused-pass artifacts such as behavior map, proof map, schema-risk inventory, referential-constraint inventory, schema consistency table, operation matrix, contract-identifier/generator-impact scan, access-control trace, or state-transition view.

Invalid evidence includes:
- vague impressions;
- generic best-practice claims;
- unproven architecture claims;
- style preference;
- speculative production impact without realistic changed-surface path.

If evidence is incomplete but merge-relevant, use Risk hypothesis or Question.

If evidence is weak and non-blocking, omit the issue or move it to Non-blocking.

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

Suggested code is optional. Include it only when the fix is small, local, precise, unambiguous, and non-speculative.

## Tests

Tests must specify:
- behavior or risk;
- test level;
- scenario;
- expected observable result.

Do not write:
- `Add tests`;
- `Increase coverage`;
- `Test this`;
- `Add unit tests`.

Prefer behavior/risk-oriented tests through public use-case, API, contract, consumer, or integration boundaries.

Use implementation-detail tests only when the implementation detail is the contract or risk boundary.

`Tests: Not required` is allowed only with a concrete reason.

`Tests: Not required` is almost never acceptable for correctness, security, data, API, runtime, transaction, migration, concurrency, idempotency, or rollout findings.

Do not create a separate missing-tests finding when the test gap belongs to another production-code finding.

## Categories

Use only these top-level categories:
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

Verdict values:
- APPROVE;
- COMMENT_ONLY;
- REQUEST_CHANGES.

Derive verdict mechanically:
- REQUEST_CHANGES: any CRITICAL/HIGH/MEDIUM finding with `Fix timing: Must fix in this PR`.
- COMMENT_ONLY: only LOW/NIT findings, Optional items, or Can be follow-up items.
- APPROVE: no findings, no merge-relevant questions, no useful non-blocking items.

Do not APPROVE with unresolved merge-relevant questions.

Do not REQUEST_CHANGES for optional cleanup, preferences, NITs, or follow-up-only items.

`Highest severity` must equal the highest reported severity.

Use `Highest severity: NONE` only when there are no findings, questions, or non-blocking items.

## Output format

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
```

Omit empty sections.

Omit Summary for clean APPROVE.

Omit Review completeness unless partial.

Do not output positive notes, praise, generic advice, process commentary, or empty ritual sections.

Do not repeat every finding in Summary.

## Audit mode output

When audit mode is requested, output normal review first, then compact focused-pass coverage matrix.

Coverage statuses:
- Checked;
- Not applicable;
- Finding;
- Question;
- Not fully reviewable.

For each triggered pass, include:
- trigger evidence;
- artifact built;
- status;
- finding/question references.

Reference existing finding numbers.

Do not create duplicate findings from the matrix.

## Final self-check

Before final output, verify:
- verdict and highest severity match findings;
- APPROVE has no unresolved merge-relevant questions;
- REQUEST_CHANGES is not used only for LOW/NIT/Optional/follow-up items;
- every triggered focused pass was completed, marked not applicable, represented by a finding/question, or marked partial;
- no focused pass was skipped because another domain produced a stronger finding;
- required evidence artifacts were built internally when their focused passes were triggered;
- every CRITICAL/HIGH/MEDIUM finding has type, confidence, evidence, required change, fix timing, tests, and category;
- no HIGH/CRITICAL finding has low confidence;
- every Verified finding has concrete evidence;
- every Risk hypothesis states uncertainty;
- every Question affects merge readiness;
- every Required change is proportional, observable, and root-cause oriented;
- every Tests field is concrete or `Not required` with a concrete reason;
- category is from the closed list;
- same-root-cause findings are grouped;
- no finding duplicates CI/linter/formatter output;
- no unrelated legacy issue is reported;
- no positive notes, generic advice, process commentary, or empty ritual sections are present;
- Summary is omitted for clean APPROVE;
- Review completeness is omitted unless partial.

Fix inconsistencies before returning the review.
