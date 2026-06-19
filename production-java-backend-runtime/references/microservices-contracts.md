# Microservices Contracts

## Load this when

Load this reference for HTTP/gRPC APIs, events, message schemas, client integrations, auth contracts, error semantics, backward compatibility, versioning, or service boundaries.

## Non-negotiable checks

Treat these as contracts:

- Public HTTP/gRPC APIs.
- Request/response fields.
- Message/event schemas.
- Error codes and error response shape.
- Auth and authorization semantics.
- Config keys consumed externally.
- Metrics used by dashboards/alerts.
- Database schema consumed outside the service.
- Externally observed behavior.

Preserve backward compatibility by default.

Do not remove, rename, narrow, or change semantics of public fields, endpoints, events, or errors without an explicit migration plan.

Prefer additive changes.

## Preferred approach

For API changes:

- Add optional fields instead of replacing required fields.
- Preserve old fields during migration.
- Version or deprecate when compatibility cannot be preserved.
- Keep validation changes compatible unless the task requires stricter behavior.
- Preserve documented error semantics.

For events/messages:

- Prefer additive schema evolution.
- Preserve event meaning.
- Consider old and new consumers during rolling deployment.
- Ensure consumers tolerate unknown fields when format supports it.
- Make consumers idempotent when duplicates are possible.

For clients:

- Check timeouts.
- Check retry safety.
- Check idempotency.
- Check error mapping.
- Check correlation ID propagation.
- Check fallback/degradation behavior.

## Red flags

- Field rename without compatibility layer.
- Enum value removal or semantic change.
- Required field added to consumed API/event.
- HTTP status change without documented reason.
- Error body shape changed casually.
- Consumer assumes exactly-once delivery.
- Client retries non-idempotent operation without safety.
- Internal API treated as unauthenticated or unauthorized.
- Breaking change hidden inside refactoring.

## Verification

Prefer:

- Existing contract tests.
- API/controller tests for response compatibility.
- Serialization/deserialization tests.
- Consumer/provider tests when repo supports them.
- Backward-compatible event fixture tests.
- Negative auth tests for protected resources.

## Final response notes

Mention:

- Contracts touched.
- Compatibility strategy.
- Migration/deprecation notes.
- Contract tests run or not available.
