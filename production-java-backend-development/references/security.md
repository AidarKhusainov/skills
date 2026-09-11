# Security and Privacy

## Decision rules

Identify the trust boundary and authorization invariant before changing security-sensitive behavior.

This reference owns application trust, authentication/authorization, tenancy/object ownership, secret handling, sensitive-data exposure, and security audit requirements. Kubernetes owns workload/container privilege mechanics.

- Enforce authorization server-side at the operation and object/tenant boundary actually being protected.
- User-controlled object identifiers require object/tenant ownership checks when access is scoped by ownership.
- Treat internal/admin/service-to-service endpoints as authenticated/authorized unless repository architecture explicitly establishes another trust model.
- Preserve least privilege in application credentials and permissions.
- Validate/normalize untrusted input at the system boundary; prefer allow-lists where validation itself is security-sensitive.
- Preserve required audit events for privileged or security-sensitive actions when the repository has an audit trail; capture actor/action/target/outcome without leaking secrets or unnecessary sensitive data.
- Never introduce secret values into source, fixtures, images, logs, errors, metrics, or traces.
- Minimize sensitive-data exposure and preserve established masking/redaction.
- Avoid returning internal implementation/security details in externally visible errors.

Do not rely on client-side checks, UI visibility, obscurity, sequential IDs, internal-network location alone, or naming such as "admin" as an authorization mechanism.

## Failure modes

Watch for:

- object lookup by user-controlled ID without ownership/tenant enforcement;
- widened role/scope/CORS/CSRF posture as incidental refactoring;
- authorization differences between equivalent HTTP/message/job entry points;
- privileged/security-sensitive state changes bypassing an established audit trail;
- missing negative tests for wrong tenant/owner/role;
- secret/config dumping in diagnostics;
- path/file/object access derived from unchecked user input;
- sensitive fields added to logs, exceptions, DTOs, metrics, or traces.

## Verification

Prove both allowed and denied behavior at the relevant security boundary. Include wrong-owner/wrong-tenant/missing-role cases when applicable. Verify required audit emission for changed privileged/security-sensitive actions when auditability is part of repository behavior. Use repository-provided secret/security scans as supporting evidence when available; they do not replace authorization behavior tests.
