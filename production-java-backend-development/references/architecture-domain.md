# Architecture and Domain Boundaries

## Decision rules

Use Clean Architecture and DDD concepts to preserve ownership and dependency direction, not to create layers or abstractions mechanically.

### Domain ownership and invariants

- Keep each business invariant owned by one clear aggregate, domain policy/service, or application use case rather than duplicated across entry points.
- Prevent invalid domain states at the boundary that owns the invariant; do not rely on UI/client validation for server-side rules.
- Model state transitions explicitly when allowed previous states matter.
- Keep aggregate consistency boundaries aligned with actual atomic business invariants; do not enlarge a transaction merely to make multiple aggregates look synchronous.
- Across bounded contexts/modules, interact through explicit owned contracts or application boundaries rather than another context's internal classes/tables/domain model.
- Do not duplicate another context's domain rules locally when the source context owns the decision.
- Use the established ubiquitous language in domain/application names and contracts; avoid generic or conflicting terminology when it hides ownership, lifecycle, or state semantics.

### Application/use-case boundaries

- Entry points such as controllers, listeners, schedulers, jobs, and internal APIs should converge on the same owned use-case/policy when they perform the same business action.
- Keep orchestration in the application/use-case boundary and business decisions in domain policy/model where those decisions have independent domain meaning.
- Persistence, transport, serialization, messaging, framework configuration, and Kubernetes/runtime details remain outer concerns and should not become domain dependencies.
- Return/use boundary models appropriate to the layer; avoid leaking persistence entities or infrastructure objects as application/domain contracts without explicit intent.

### Dependency direction and abstractions

- Preserve dependency direction toward domain/application policy.
- Add ports/interfaces when they create a real boundary, isolation, replaceability, or test seam—not merely because a layer diagram expects them.
- Avoid pass-through services/use cases, DTO/mapping proliferation, `Manager`/`Helper` abstractions with unclear ownership, and competing patterns for the same responsibility.
- Preserve established module/bounded-context ownership unless the task intentionally changes it; architecture migration should be explicit and scoped.
- When intentionally changing a documented boundary or architectural convention, keep the governing ADR/module documentation aligned when the repository treats it as a source of truth.

## Failure modes

Watch for:

- business decisions in controllers, repositories, listeners, schedulers, mappers, or configuration;
- the same command handled differently across HTTP/message/job paths without domain reason;
- domain/application code depending on Spring, JPA implementation details, HTTP, Kafka, filesystem, or deployment configuration where that dependency is not the domain boundary itself;
- direct reads/mutations of another bounded context's internal state;
- state transitions that skip required prior-state checks;
- multiple aggregate roots forced into one transaction without a real consistency invariant;
- application services accumulating domain policy or infrastructure mechanics instead of orchestration;
- cyclic module/package dependencies or new abstractions added for hypothetical future flexibility.

## Verification

Prefer behavior tests through domain/application seams. Use architecture/module-boundary tests when the repository already provides them. Verify equivalent public entry points preserve the same domain rules when a rule is shared across transports.
