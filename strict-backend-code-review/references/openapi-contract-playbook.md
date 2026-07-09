# OpenAPI and Contract Playbook

Load this file when the changed surface touches OpenAPI, protobuf, generated API interfaces, public DTOs, controller interfaces, externally consumed schemas, request/response contracts, or generated-client compatibility.

## Goal

Review the external machine-readable contract and generated-client/tooling stability, not only controller code.

Treat machine-readable schemas as public contract inputs unless repo-local context proves they are internal-only.

## Required workflow

1. List changed operations, schemas, parameters, request bodies, responses, security requirements, and generated interfaces.
2. Build an operation matrix.
3. Build a contract-identifier and generator-impact scan.
4. Cross-check controller/interface behavior, validation, tests, and generated types when present.
5. Report only findings with concrete contract evidence tied to changed surface.

## Required artifacts

Build these artifacts internally. Print them only in audit mode or when needed as evidence for a finding/question.

### Operation matrix

```text
operationId -> method/path -> inputs -> required fields -> documented outcomes -> security requirements -> generated types/refs -> proof in tests
```

Use this to check outcome completeness, validation semantics, security behavior, write semantics, and controller/contract alignment.

### Contract-identifier and generator-impact scan

```text
contract identifier -> role -> naming/stability pattern -> referenced by -> generator/tooling risk -> notes
```

Use this when public or generated identifiers are added, renamed, regenerated, moved, or mixed with another convention.

## Checks

Report when the PR:
- changes externally observed request, response, status, error, security, validation, nullability, required-field, enum, or schema behavior without contract alignment;
- changes documented outcome semantics without preserving or explaining affected client-visible behavior;
- marks inputs as required without corresponding validation/error semantics and tests;
- changes protected operations without preserving documented or expected security-failure behavior;
- changes public/generated contract identifiers in a way that can destabilize references, generated artifacts, documentation, or client usage;
- treats public/generated contract identifiers as local style when they affect compatibility or tooling;
- exposes internal state, sensitive data, server-controlled fields, or implementation-only statuses through public DTOs/schemas;
- misrepresents operation outcome, durability, partial completion, or failure semantics;
- changes controller behavior, generated interfaces, machine-readable contracts, or contract tests without keeping the others aligned;
- changes error shape, retryability, idempotency, pagination, sorting, filtering, or representation semantics without compatibility evidence.

## Response and outcome guidance

Do not require an exhaustive outcome catalog for every operation.

Require documented outcomes only when they are part of a credible changed contract path. Use repo-local error-envelope and status conventions when available.

When the API is internal-only, still check generated clients and caller expectations if the contract is machine-read by another module or service.

## Evidence requirements

For outcome findings, name:
- operationId and method/path;
- current documented outcomes;
- changed input/security/failure path that makes the missing or changed outcome contract-relevant;
- expected documented status, outcome, or error envelope according to local convention.

For contract-identifier findings, name:
- inconsistent or unstable identifiers;
- generated/client/tooling consequence;
- why this is not style-only.

For controller/contract mismatch findings, name:
- controller/interface method;
- contract operation or schema;
- exact mismatch in request, response, status, validation, or error behavior.

## Tests and verification

Prefer contract and boundary tests:
- contract validation or generation check;
- generated-client compilation or snapshot check when repo workflow supports it;
- API/integration test for documented success and failure paths;
- validation test for required/missing/null/invalid inputs;
- denied/security-failure test for protected operations;
- compatibility test for old/new clients when relevant.

## Findings to avoid

Do not report:
- wording preferences in descriptions or summaries;
- local naming taste without generated/public contract impact;
- hypothetical outcomes with no credible changed behavior path;
- speculative generator concerns without changed schema references or generation workflow;
- unrelated legacy contract incompleteness not touched, depended on, exposed, or materially amplified by the PR.
