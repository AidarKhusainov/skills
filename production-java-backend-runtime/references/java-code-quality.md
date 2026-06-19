# Java Code Quality

## Load this when

Load this reference for Java source changes, refactoring, naming, error handling, validation, domain logic, data structures, concurrency, or code smell cleanup.

## Non-negotiable checks

- Preserve observable behavior unless intentionally changed.
- Prefer clarity over cleverness.
- Prefer simple, explicit code over premature abstraction.
- Keep changes localized.
- Improve touched code when low-risk and task-relevant.
- Avoid broad cleanup mixed with business logic changes.
- Do not copy local bad practice merely for consistency.
- Do not introduce hidden global state, temporal coupling, or surprising side effects.
- Do not swallow exceptions without clear recovery or diagnostics.
- Do not leak sensitive data in exceptions, logs, or messages.

## Preferred approach

Use strong names:

- Classes name concepts.
- Methods name behavior.
- Variables name domain facts, not types.
- Tests describe observable behavior.

Prefer:

- Immutability for values and DTO-like data.
- Constructor injection over mutable field injection.
- Small methods with one reason to change.
- Explicit validation at boundaries.
- Domain-specific exceptions or error models when repository conventions support them.
- Package-private visibility for implementation details.
- Standard library types before custom abstractions.

Avoid:

- Utility-class sprawl.
- Static mutable state.
- Boolean parameters that obscure behavior.
- Long parameter lists.
- Large methods with mixed abstraction levels.
- Nulls as control flow when Optional, validation, or explicit branching is clearer.
- Catch-all exception handling without intent.
- Deep nesting when guard clauses improve readability.
- Interfaces with a single implementation unless they define a real boundary.

## Bounded improvement policy

When touching poor code:

- Clean only the area needed for the task.
- Extract behavior only when it improves readability or testability.
- Rename only when local and safe.
- Do not reformat unrelated files.
- Do not convert entire packages to a new style.
- Mention broader cleanup separately.

## Red flags

- Business logic hidden in controllers, repositories, mappers, schedulers, or listeners.
- Repository calls scattered across multiple layers without a clear use case.
- DTOs used as mutable domain models.
- Repeated validation or authorization logic.
- Implicit behavior controlled by string constants or magic values.
- Reflection, dynamic class loading, or annotation magic without tests.
- Concurrency without clear ownership, lifecycle, or thread-safety.
- Time, randomness, or external state used without injectable seams.

## Verification

For Java code changes, prefer:

- A narrow unit or behavior test for changed logic.
- Compile check for touched module.
- Static analysis or formatter only if repository uses it.
- Broader module check when the change affects public behavior.

## Final response notes

Mention:

- Any local improvement performed.
- Any poor surrounding code left intentionally out of scope.
- Any behavior that remains unverified.
