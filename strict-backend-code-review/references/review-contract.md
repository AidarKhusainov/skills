# Review Contract Reference

Load this reference only when a candidate finding, uncertainty, or output edge case is ambiguous. Routine reviews should use the contract in `SKILL.md` without loading this file.

## Finding vs uncertainty

A published finding is an evidence-backed assertion.

Use `Unresolved questions` when a specific missing answer could materially change whether a problem exists or how the changed behavior should be interpreted.

Use `Review limitations` when unavailable context or verification prevents reliable review of part of the changed surface.

Do not turn an important but unverified risk into a finding. Importance does not replace evidence.

## Problem vs Evidence

Keep `Problem` self-contained by default.

Use the optional `Evidence` field only when substantial proof would make `Problem` harder to read, such as several control-flow steps, old/new contract combinations, multiple config/runtime surfaces, or a compact focused-pass artifact.

Do not repeat the same facts in both fields.

## Expected vs implementation

`Expected` is a resolution criterion, not a patch instruction.

An implementation detail may appear in `Expected` only when that detail is itself the externally required contract or invariant.

Do not weaken an established requirement by presenting scope reduction or acceptance-criteria changes as an equally valid expected outcome. If the requirement itself is uncertain, represent that uncertainty separately.

## Missing tests

Do not create a separate test finding merely because coverage could be higher.

A missing-test finding is appropriate when changed behavior or an invariant lacks meaningful proof and that proof gap independently crosses the finding threshold.

When a test gap is evidence for another defect, keep it with that finding.

## No-finding output with uncertainty

Use `No findings.` only when the review has no findings, unresolved questions, or material limitations.

If no finding survives but unresolved questions or review limitations remain, output those sections and end with:

```text
No findings in the reviewed scope.
```

This distinguishes a clean review from one whose conclusion is constrained by unresolved evidence.
