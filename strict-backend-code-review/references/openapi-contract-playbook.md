# OpenAPI and Contract Playbook

Load this file when the changed surface touches OpenAPI, protobuf, generated API interfaces, public DTOs, controller interfaces, externally consumed schemas, request/response contracts, or generated-client compatibility.

## Goal

Review the external machine-readable contract and generated-client/tooling stability, not only controller code.

Treat OpenAPI/protobuf schemas as public contract inputs unless repo-local context proves they are internal-only.

## Required workflow

1. List changed operations, schemas, parameters, request bodies, responses, security requirements, and generated interfaces.
2. Build an operation matrix.
3. Build a schema naming and generator-impact scan.
4. Cross-check controller/interface behavior, validation, tests, and generated types when present.
5. Report only findings with concrete contract evidence tied to changed surface.

## Required artifacts

Build these artifacts internally. Print them only in audit mode or when needed as evidence for a finding/question.

### Operation matrix

```text
operationId -> method/path -> inputs -> required fields -> success responses -> known error responses -> auth/authz responses -> generated types/refs -> proof in tests
```

Use this to check response completeness, validation semantics, auth failures, write semantics, and controller/OpenAPI alignment.

### Schema naming and generator-impact scan

```text
schema/type name -> role -> naming pattern -> referenced by -> generator/tooling risk -> notes
```

Use this when schema names are added, renamed, regenerated, moved, or mixed with another naming convention.

## Checks

Report when the PR:
- changes externally observed request, response, status, error, auth, validation, nullability, required-field, enum, or schema behavior without contract alignment;
- documents only a success response when changed inputs, validation, auth/authz, dependency failures, or known business failures make non-success behavior part of the usable contract;
- marks request fields as required without corresponding validation/error semantics and tests;
- changes protected operations without documenting or preserving authentication/authorization failure behavior when the API contract includes those statuses;
- mixes schema naming conventions for generated/public schemas in a way that can destabilize generated class names, references, docs, or client searchability;
- uses schema/type names as if they were local style when they are generator inputs or public contract identifiers;
- exposes persistence entities, internal domain state, secrets, PII, server-controlled fields, or implementation-only statuses through public DTOs/schemas;
- returns success for an unimplemented, non-durable, partial, or ambiguous write path;
- changes controller behavior, generated interfaces, OpenAPI, or contract tests without keeping the others aligned;
- changes error shape, retryability, idempotency, pagination, sorting, filtering, or timezone semantics without compatibility evidence.

## Response completeness guidance

Do not require an exhaustive status catalog for every operation.

Require documented non-success responses when they are part of the credible changed contract, for example:
- invalid input or missing required fields;
- authentication or authorization failure for protected APIs;
- not found / conflict / idempotency conflict when exposed by endpoint semantics;
- dependency or server failure when the contract documents operational errors;
- default error envelope used by generated clients.

When the API is internal-only, still check generated clients and caller expectations if the contract is machine-read by another module or service.

## Evidence requirements

For response findings, name:
- operationId and method/path;
- current documented responses;
- changed input/auth/failure path that makes the missing response contract-relevant;
- expected documented status or default error envelope.

For schema naming findings, name:
- inconsistent schema names;
- generated/client/tooling consequence;
- why this is not style-only.

For controller/OpenAPI mismatch findings, name:
- controller/interface method;
- OpenAPI/protobuf operation or schema;
- exact mismatch in request, response, status, validation, or error behavior.

## Tests and verification

Prefer contract and boundary tests:
- OpenAPI/protobuf validation or generation check;
- generated-client compilation or snapshot check when repo workflow supports it;
- API/integration test for success and documented failure paths;
- validation test for required/missing/null/invalid inputs;
- denied/auth failure test for protected operations;
- compatibility test for old/new clients when relevant.

## Findings to avoid

Do not report:
- wording preferences in descriptions or summaries;
- local naming taste without generated/public contract impact;
- hypothetical statuses with no credible changed behavior path;
- speculative generator concerns without changed schema references or generation workflow;
- unrelated legacy contract incompleteness not touched, depended on, exposed, or materially amplified by the PR.
