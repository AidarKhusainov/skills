# Security

## Load this when

Load this reference for authentication, authorization, tenancy, object ownership, confidential data, input validation, admin APIs, service-to-service calls, file/object access, container hardening, or Kubernetes security posture.

## Non-negotiable checks

Treat these as production contracts:

- Authentication.
- Authorization.
- Tenant isolation.
- Object ownership.
- Confidential configuration.
- Sensitive data.
- Auditability.
- Runtime privileges.

Identify trust boundaries before changing:

- APIs.
- Jobs.
- Message handlers.
- Admin operations.
- File/object storage access.
- Service-to-service calls.
- Webhooks.
- Batch imports.

Enforce object-level authorization for user-controlled object IDs.

Do not rely on:

- Client-side checks.
- UI visibility.
- Obscurity.
- Sequential IDs.
- Internal network location alone.
- "Admin" naming without authorization checks.

## Preferred approach

For authorization:

- Check user role and object ownership/tenant.
- Add negative tests for forbidden user, wrong tenant, missing role, and invalid ownership.
- Preserve method-level or endpoint-level authorization.
- Treat internal APIs as requiring explicit authorization unless proven otherwise.

For input:

- Validate and normalize at system boundaries.
- Prefer allow-lists for security-sensitive validation.
- Use repository-approved safe query and file/object-access APIs.
- Avoid ad hoc parsing or unchecked user-controlled paths.

For confidential configuration:

- Never introduce confidential values in source code, tests, configs, Dockerfiles, images, or logs.
- Use repository-approved secret management.
- Avoid printing environment or config values that may contain confidential data.

For sensitive data:

- Minimize data exposure.
- Redact logs.
- Preserve existing masking.
- Avoid returning internal details in errors.

For runtime:

- Use least privilege for service accounts, DB users, cloud permissions, containers, and Kubernetes workloads.
- Avoid privileged containers, root users, broad capabilities, host namespaces, writable root filesystems, and overbroad mounted confidential data unless justified.

## Red flags

- Endpoint fetches object by ID without ownership check.
- Admin/internal API has weak or missing authorization.
- Security behavior changed without tests.
- CORS widened casually.
- CSRF posture changed casually.
- Sensitive data added to logs, metrics, or traces.
- Confidential values added to config or test fixtures.
- Container made privileged to work around permission issue.
- Service account permissions broadened without scope.

## Verification

Prefer:

- Negative authorization tests.
- Tenant isolation tests.
- Input validation tests.
- Security slice/integration tests.
- Repository-provided confidential-data scan if available.
- Manifest/security-context review for runtime changes.

## Final response notes

Mention:

- Security boundary touched.
- Authorization/tenancy assumptions.
- Negative tests added/run.
- Any unverified security risk.
