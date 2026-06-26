# Security, Privacy, and Observability Review Reference

Load this file only when the changed surface touches authentication, authorization, tenant isolation, object ownership, secrets, PII, redaction, audit, logging, metrics, tracing, error responses, security-sensitive domain actions, or production diagnosability.

## Review focus

Check whether the PR preserves:
- authentication and authorization boundaries;
- object-level and function-level access control;
- tenant/user ownership enforcement;
- least privilege;
- privacy and data minimization;
- secret safety;
- safe error disclosure;
- auditability of security-sensitive operations;
- production diagnosability without sensitive-data leakage.

## Authentication and authorization

Report when the PR:
- adds or changes an endpoint without clear authn/authz protection;
- relies on controller routing, UI hiding, gateway path, or client behavior as authorization;
- performs object access based on user-controlled IDs without ownership check;
- changes role/scope/permission semantics without denied-path tests;
- bypasses the established authorization layer in a new entry point;
- authorizes at list/search level but not at object/action level;
- checks ownership after state mutation or external side effect;
- creates admin/internal endpoints without network, identity, and authorization evidence;
- changes service-to-service calls without caller identity or permission model.

Required change should name the authorization boundary and denied behavior.

## Tenant isolation

Report when the PR:
- queries data without tenant/account/organization predicate in tenant-scoped flows;
- accepts tenant/user/account IDs from request without verifying caller membership;
- stores or caches tenant-scoped data under non-tenant-scoped keys;
- emits events or logs that mix tenants without partitioning or access control;
- changes background jobs to process cross-tenant data without isolation checks;
- changes repository methods from scoped to unscoped access.

Tests should prove allowed and denied cross-tenant/object access.

## Input handling and mass assignment

Report when the PR:
- binds client DTOs directly into persistence/domain objects in sensitive flows;
- makes server-controlled fields client-writable;
- accepts unknown or nested fields that can modify unauthorized state;
- changes validation so invalid state reaches domain/persistence;
- trusts IDs, roles, prices, ownership, status, or permissions from the client;
- changes deserialization in a way that enables unsafe polymorphism or hidden fields.

Prefer explicit command/input models for state-changing operations.

## Privacy and sensitive data

Report when the PR:
- adds PII/secrets/tokens/credentials to API responses, events, logs, metrics, traces, errors, or audit messages;
- expands data returned to clients without need-to-know reasoning;
- stores sensitive data without encryption/tokenization/hash policy evidence when required by project context;
- changes retention, deletion, anonymization, or export behavior;
- exposes internal identifiers that can enable enumeration or correlation;
- includes request/response bodies in logs/traces without redaction.

Required change should minimize data exposure at source, not only hide it in UI.

## Secrets and credentials

Report when the PR:
- adds secrets to repository files, examples, tests, manifests, images, logs, or error messages;
- moves secrets into config maps, plain env vars, or defaults without secret-management reasoning;
- changes secret names/paths without deployment wiring;
- logs credentials, Authorization headers, cookies, tokens, signed URLs, or connection strings;
- builds URLs or commands with secrets that can appear in process lists, traces, or exceptions.

Do not report generic secret-management preference without changed secret surface.

## Error responses

Report when the PR:
- leaks stack traces, SQL errors, internal class names, infrastructure details, account existence, or authorization logic;
- maps security failures to success or ambiguous business errors;
- changes error status codes in a way that breaks clients or hides retryability;
- returns different error shapes for missing vs unauthorized resources when that enables enumeration and the project avoids it;
- loses correlation/request IDs needed for support.

## Audit logging

Report when the PR changes security-sensitive operations without audit evidence.

Security-sensitive operations include:
- permission/role changes;
- account/tenant membership changes;
- money/payment/refund actions;
- PII export/delete/update;
- admin actions;
- authentication or credential lifecycle changes;
- policy/state transitions with compliance impact.

Audit events should include actor, target, action, result, timestamp, correlation/request ID, and safe context.

Do not include secrets or excessive PII in audit events.

## Logs, metrics, and traces

Report observability gaps when they affect production diagnosability of changed risky paths.

Check:
- structured logs for important state transitions and failures;
- correlation/request/trace IDs across service boundaries;
- metrics for new consumers, schedulers, external calls, migrations, and critical business operations;
- tracing spans around new remote calls or async boundaries;
- error labels that distinguish retryable/non-retryable outcomes;
- redaction of sensitive fields;
- cardinality safety for labels/tags;
- no logging inside tight loops at unsafe volume.

Do not require observability for trivial local refactors with no runtime behavior change.

## Negative space

For security/privacy/observability changes, check missing:
- denied-path tests;
- ownership/tenant isolation checks;
- audit event for sensitive operation;
- redaction of new sensitive fields;
- secret wiring in deployment config;
- standard error mapping;
- metrics/logs/traces for new critical runtime path;
- authorization documentation or policy update.

## Findings to avoid

Do not report:
- generic security advice without changed attack surface;
- speculative PII risk without data path evidence;
- audit/logging requests for low-risk code with no production diagnosability need;
- scanner output duplication without root-cause review value;
- unrelated legacy auth problems not touched or amplified by the PR.
