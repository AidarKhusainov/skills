# AGENTS.md Adapter Template for Java Backend Repositories

Use this template to create or update the repository-level `AGENTS.md`.

Keep it short, factual, and repository-specific.
Do not duplicate generic engineering principles from the reusable skill.
Prefer commands and conventions that are actually used by this repository.

## Project overview

- Service name:
- Business/domain purpose:
- Main runtime:
- Main framework:
- Java version:
- Build tool:
- Deployment target:
- Primary database:
- Messaging/eventing:
- External dependencies:

## Repository layout

- Main application module:
- Domain/application code:
- Web/API adapters:
- Persistence adapters:
- Messaging adapters:
- Configuration:
- Migrations:
- Tests:
- Deployment/runtime artifacts:
- Generated code:

## Commands

Prefer repository wrappers.

### Build

```bash
# TODO
```

### Unit tests

```bash
# TODO
```

### Integration tests

```bash
# TODO
```

### Static checks / formatting

```bash
# TODO
```

### Migration validation

```bash
# TODO
```

### OpenAPI / generated code

```bash
# TODO
```

### Docker / Kubernetes / Helm render

```bash
# TODO
```

## Local development

- Required services:
- Local profiles:
- Required environment variables:
- Testcontainers usage:
- Docker Compose usage:
- Known local setup caveats:

## Coding conventions

- Package/module conventions:
- Naming conventions:
- Error handling conventions:
- API response conventions:
- Logging conventions:
- Transaction conventions:
- Test naming/style conventions:
- Mocking conventions:

## Architecture notes

- Architectural style:
- Module boundaries:
- Dependency direction:
- Domain language / glossary location:
- ADR location:
- Public contracts:
- Internal-only APIs:
- Generated-code boundaries:

## Runtime and deployment notes

- Health endpoints:
- Readiness/liveness/startup probe behavior:
- Graceful shutdown assumptions:
- Resource requests/limits policy:
- Config/secrets source:
- Service ports:
- Background workers / consumers:
- Migration/init job behavior:

## Security notes

- Authentication model:
- Authorization model:
- Tenant/user ownership rules:
- Sensitive data handling:
- Secret handling:
- Security tests to preserve:

## Testing policy for this repo

- Preferred narrow test:
- Preferred Spring slice tests:
- Preferred integration test style:
- Contract test location:
- Tests that are slow/flaky:
- When full test suite is required:

## Pull request expectations

Before final response, report:

- What changed.
- What was verified.
- What was not verified.
- Any residual risks.
- Any follow-up refactoring opportunities.
