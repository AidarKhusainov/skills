# Code Review Output Examples

Load this file only when an example is needed to resolve output ambiguity.

Use `SKILL.md` as the source of truth. Do not copy examples mechanically.

Keep examples synthetic. Do not copy private repository names, workstation paths, internal ticket IDs, company-specific package names, or other user-specific identifiers into this public skill.

## Finding with separate Evidence

```text
Findings:

1. PATCH cannot distinguish omitted fields from explicit null

   Location:
   - backend/src/main/java/com/example/customer/api/CustomerPatchRequest.java:18
   - backend/src/main/java/com/example/customer/CustomerMapper.java:34 — toCommand
   - backend/src/test/java/com/example/customer/api/CustomerControllerTest.java:72

   Problem:
   The changed PATCH contract collapses omitted and explicitly null fields into
   the same command value, so the application cannot reliably preserve an omitted
   field while allowing an explicit null to clear it.

   Expected:
   Omitted fields must preserve existing values, while explicit null must retain
   its contract-defined meaning for fields that may be cleared.

   Evidence:
   CustomerPatchRequest represents both states with nullable fields, and
   CustomerMapper#toCommand maps both to null. Existing controller tests cover
   only requests where all fields are present.

   Validation:
   Verify omitted, explicit-null, and concrete-value requests produce distinct
   persisted outcomes where the API contract distinguishes those cases.
```

Use a separate `Evidence` field only when the proof would make `Problem` harder to read.

## Negative-space finding from explicit intent

```text
Findings:

1. Required collection-response migration is incomplete

   Location:
   - docs/plans/response-contract-migration.md:42
   - backend/src/integration-test/java/com/example/api/ResponseContractITest.java:15
   - frontend/src/api/resources.ts:28

   Problem:
   The current acceptance criteria require the endpoint and consumer path to use
   the collection response, but the integration test remains disabled and the
   frontend model still expects a single object. The current change therefore
   does not establish the required end-to-end contract.

   Expected:
   The endpoint and affected consumers must consistently use the collection
   contract required by the current acceptance criteria.

   Validation:
   Exercise the endpoint and consuming contract with a collection response and
   verify both sides interpret the same response shape.
```

This reports the required observable state without choosing which technical surface must change. If the acceptance criteria themselves are uncertain, represent that uncertainty separately.

## Uncertainty without a finding

```text
Unresolved questions:
- Is `fulfillmentMode` versioned, defaulted, or otherwise backward-compatible for
  existing consumers outside this repository? The available producer changes do
  not establish that behavior.

Review limitations:
- Production deployment manifests were unavailable, so probe and shutdown
  behavior for the changed consumer could not be verified.

No findings in the reviewed scope.
```

Questions identify a material missing answer. Limitations identify unavailable context or verification. Neither implies a defect by itself.

## Expected boundary

Good:

```text
Expected:
A redelivered event with the same identity must not commit a second state
transition.
```

Too prescriptive:

```text
Expected:
Create an inbox table with a unique event_id column.
```
