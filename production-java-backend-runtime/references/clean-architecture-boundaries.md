# Clean Architecture and Boundaries

## Load this when

Load this reference for architecture boundaries, module/package design, dependency direction, domain logic placement, ports/adapters, refactoring, layering, or architectural cleanup.

## Non-negotiable checks

Use Clean Architecture as dependency direction, not folder ceremony.

The goal is:

- Dependency direction toward domain/application policy.
- Framework independence where valuable.
- Testability.
- Locality.
- Clear domain language.
- Stable public seams.
- Infrastructure isolated from business rules.

Do not introduce architecture ceremony mechanically.

## Preferred approach

Keep business rules in:

- Domain model.
- Domain services.
- Application/use-case services.
- Policy classes.

Keep these as adapters or outer details:

- Controllers.
- Message listeners.
- Schedulers.
- Repositories.
- REST/gRPC clients.
- DTO mappers.
- Persistence entities.
- Framework configuration.
- Kubernetes/runtime config.

Use ports/adapters when they improve:

- Testability.
- Isolation.
- Replaceability.
- Dependency direction.
- Boundary clarity.
- External system integration.

Avoid:

- Interfaces with no boundary value.
- Use-case classes that only rename service methods.
- Pass-through layers.
- DTO explosion without contract value.
- Mapping layers that add no isolation.
- Domain logic leaking into controllers/repositories/listeners.

## Bounded improvement

In existing codebases:

- Preserve architectural intent when it exists.
- Improve dependency direction locally in touched code.
- Extract domain behavior only when it reduces coupling or improves tests.
- Do not migrate the whole package structure unless requested.
- Report broader architecture opportunities separately.

## Red flags

- Controller owns business workflow.
- Repository owns business decisions.
- Listener contains policy logic.
- Domain class depends on Spring, JPA, HTTP, Kafka, or filesystem details.
- Multiple modules reach into each other's internals.
- Cyclic package/module dependencies.
- "Manager" or "Helper" classes with unclear responsibility.
- Shallow service that just delegates without owning behavior.
- New abstraction added "for future flexibility" without current leverage.

## Verification

Prefer:

- Unit tests through domain/application seams.
- Architecture tests if repo uses them.
- Module boundary tests where available.
- Compile/static checks for dependency direction.
- Integration tests for adapters.

## Final response notes

Mention:

- Boundary improved.
- Dependency direction preserved or improved.
- Any architecture cleanup left as follow-up.
