# Java Code Quality

## Decision rules

Keep Java changes explicit about ownership, lifecycle, and failure semantics.

- Preserve observable behavior unless the task intentionally changes it.
- Prefer standard language/library types over custom abstractions when they express the responsibility clearly.
- Introduce an interface, wrapper, factory, strategy, or utility only when it owns a real boundary, variation, invariant, or meaningful reduction in complexity.
- Keep mutable state ownership clear. Prefer immutable value objects/DTO-like data when mutation has no domain meaning.
- Keep time, randomness, external state, and concurrency behind controllable seams when behavior depends on them.
- Preserve exception meaning. Catch only when translating, recovering, adding actionable context, or satisfying a boundary contract.
- Keep implementation details no more visible than needed by repository conventions and tests.
- Avoid nested application types when they have independent meaning, invariants, reuse, or testing value; small class-local implementation details may remain nested.
- Keep declarations near the behavior they support and use a natural top-down reading order where practical, without mechanical reordering.

## Failure modes

Watch for:

- hidden mutable global/static state;
- temporal coupling or lifecycle assumptions not enforced by the API;
- catch-all exception handling that loses failure semantics;
- `null`, boolean flags, or magic strings obscuring domain states or behavior choices;
- large methods mixing policy, orchestration, persistence, mapping, and transport concerns;
- reflection/annotation magic whose behavior is not evident or protected;
- shared mutable state without explicit thread-safety/ownership;
- ad-hoc executors, locks, or parallelism without bounded lifecycle;
- generated `equals`/`hashCode`/`toString` behavior that is unsafe for mutable entities, associations, proxies, or sensitive fields;
- abstractions introduced only for hypothetical future flexibility.

## Verification

Use the narrowest behavioral test that proves changed Java semantics. Add concurrency/lifecycle-specific tests when those semantics are part of the change. Compile/static analysis is useful supporting evidence, not a substitute for behavior proof.
