# External Contracts

## Decision rules

Treat externally consumed behavior as a contract when another service, client, operator, or persisted message can depend on it. This includes public/internal service APIs, request/response fields, event/message schemas, externally interpreted error semantics, and documented configuration consumed across ownership boundaries.

Preserve compatibility by default unless the task explicitly requires a breaking change.

For a contract-affecting change, reason from:

```text
contract element -> old behavior -> new behavior -> compatibility strategy -> consumer impact -> proof
```

- Prefer additive evolution when the format permits it.
- Do not remove, rename, narrow, or reinterpret consumed fields/endpoints/events/errors without an explicit compatibility/migration strategy.
- Keep schema/source definitions, generated artifacts, implementation, fixtures, and tests aligned.
- For rolling deployments, reason about old and new producers/consumers coexisting.
- Additive fields must still respect required/optional/default/null semantics used by real consumers.
- Preserve event meaning; creating a new schema with old semantics changed underneath is still a breaking change.
- For changed API operations, check success, validation/client failure, auth/authz failure, domain conflict/not-found where applicable, dependency/server failure, and async/partial outcomes where applicable.
- Retry safety, idempotency, and failure recovery for calls belong to the resilience reference; this reference owns the externally visible semantics of those failures.

## Failure modes

Watch for:

- field or endpoint rename without compatibility support;
- enum value removal or semantic reuse;
- newly required fields for existing consumers;
- changed HTTP/gRPC status or error body semantics without intent;
- generated clients drifting from the source contract;
- consumer assumptions of exactly-once delivery;
- breaking behavior hidden inside a refactor;
- database structures used as cross-service contracts without explicit ownership.

## Verification

Prefer existing contract tests, controller/API compatibility tests, serialization fixtures, consumer/provider tests, and backward-compatible event fixtures. Verify both old and new representations when a rolling transition must support them concurrently.
