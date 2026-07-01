# Java, Spring, Testing, and Maintainability Review Reference

Load this file only when the changed surface touches Java/Spring code, Spring MVC/WebFlux, validation, transactions, proxies, JPA/Hibernate, Jackson, Lombok, configuration binding, tests, readability, naming, complexity, or local maintainability.

## Review focus

Check whether the PR preserves:
- Java/Spring framework semantics;
- transaction correctness;
- validation and binding correctness;
- persistence safety;
- serialization behavior;
- behavior-oriented tests;
- readable and simple touched code;
- local Boy Scout cleanup.

## Spring components and dependency injection

Report when the PR:
- introduces hidden dependencies or service locator access instead of explicit dependencies;
- puts business decisions into Spring configuration or bean wiring;
- creates circular dependencies or lifecycle-sensitive initialization;
- performs I/O or state mutation in constructors or `@PostConstruct` without startup failure reasoning;
- uses static mutable state for request/domain behavior;
- introduces mutable fields in singleton beans without thread-safety reasoning;
- uses profiles/conditions that change behavior without tests or config evidence.

## Transactions and proxies

Report when the PR:
- assumes `@Transactional` applies to self-invocation or private methods;
- puts transaction annotations on methods/classes where Spring proxying will not apply;
- changes propagation/isolation/rollback behavior without evidence;
- catches and swallows exceptions that should trigger rollback;
- performs remote calls inside transactions without necessity and timeout reasoning;
- publishes events or sends messages before transaction commit when consumers require committed state;
- uses read-only transactions while mutating state;
- makes transaction boundary differ across equivalent entry points.

Required change should identify the intended transactional boundary.

## Validation and binding

Report when the PR:
- accepts invalid input because `@Valid`/`@Validated` is missing at the boundary;
- relies on DTO annotations for domain invariants that must be enforced server-side;
- changes null/missing/default semantics without tests;
- uses primitive types where absence has business meaning;
- allows mass assignment of server-controlled fields;
- changes validation groups without evidence;
- maps validation failures to non-standard error responses;
- validates after mutation, persistence, or external side effect.

## Spring MVC/WebFlux APIs

Report when the PR:
- maps multiple handlers ambiguously;
- changes path/query/header/body semantics without contract tests;
- exposes internal exceptions or persistence entities;
- returns success for partial failure without documented semantics;
- changes async/reactive execution without backpressure/error handling;
- blocks reactive paths unexpectedly;
- changes controller advice/error mapping in a client-visible way;
- changes content type, status, or response shape without compatibility evidence.

## JPA/Hibernate and persistence mapping

Report when the PR:
- exposes JPA entities through API or message contracts;
- changes lazy/eager fetching in a way that can create N+1 or large graph loading;
- adds cascade/orphan removal that can delete unintended data;
- changes equals/hashCode using mutable fields or lazy associations;
- adds Lombok `@Data`/`toString` on entities with associations or sensitive fields;
- mutates detached entities without clear merge behavior;
- relies on entity callbacks for business decisions hidden from use cases;
- changes optimistic locking/version fields without conflict behavior;
- changes repository queries without checking tenant/security predicates.

## Jackson and serialization

Report when the PR:
- changes enum, date/time, BigDecimal, optional, null, or missing-field behavior;
- uses global ObjectMapper changes that affect unrelated contracts;
- adds polymorphic serialization/deserialization without safety and compatibility evidence;
- serializes lazy entities or internal domain objects unintentionally;
- changes inclusion rules and exposes previously omitted fields;
- uses annotations on domain/persistence objects to satisfy transport concerns.

## Configuration binding

Report when the PR:
- adds required config without default, Helm/env wiring, or startup failure behavior;
- changes config names without backward-compatible transition;
- moves secret-like values into non-secret config;
- changes units/timeouts without explicit type/unit naming;
- does not validate required config at startup;
- changes behavior by profile but tests only one profile;
- uses ambiguous boolean config names for risky behavior.

## Tests

Prefer behavior/risk-oriented tests.

Before accepting tests, map changed branches/guards/invariants/failure/race paths to proof, not line coverage. Focus on hidden Java behavior:
- constructors/records/enums/validators and null/missing/empty/`Optional` transitions;
- side-effect splits: outbox/inbox, persistence, events, remote calls;
- lock/reload false paths: absent, stale, already processed, claim lost;
- exception/rollback/retry/failure metrics and duplicate/concurrency/idempotency/ordering.

Report a test finding when such a path protects domain/API/serialization/transaction/persistence/idempotency/failure observability without behavioral proof. Search tests by method/factory/enum/status/exception/side effect; if tests cannot run, mark partial and map statically.

Report when tests:
- verify mocks instead of observable behavior for business-critical paths;
- assert implementation details that make safe refactoring hard;
- only cover happy path for changed risky behavior;
- omit denied, invalid, duplicate, retry, failure, or concurrent scenarios that prove the finding;
- use unrealistic mocks that bypass transaction, serialization, persistence, or security behavior under review;
- pass while not asserting the behavior named in the test;
- depend on order, wall-clock time, random data, shared state, or environment in a flaky way;
- overuse `@SpringBootTest` where a smaller boundary would prove the same behavior;
- underuse integration/contract tests where framework/runtime behavior is the risk.

Prefer tests through public use-case, API, contract, consumer, or persistence boundary when practical.

Use implementation-detail tests only when the implementation detail is the contract or risk boundary.

## Maintainability and readability

Report when the PR:
- adds nested branching that hides domain decisions;
- mixes validation, authorization, domain logic, persistence, mapping, and side effects in one method;
- duplicates decision logic already owned elsewhere;
- uses names that hide domain, security, transaction, or idempotency meaning;
- introduces generic helper abstractions before repeated need is shown;
- adds over-engineered patterns that obscure simple behavior;
- makes touched code harder to test at the behavior boundary;
- expands a legacy method in a way that makes the new rule harder to verify.

Do not report mechanical style, formatting, import ordering, or line wrapping.

## Boy Scout cleanup

Require local cleanup when:
- the PR modifies the problematic method/class;
- cleanup is necessary to make new behavior testable or understandable;
- duplication causes a realistic future correctness risk;
- extraction/renaming reduces complexity without broad redesign;
- the cleanup stays within touched or directly adjacent code.

Do not require broad rewrite for preference-only readability concerns.

## Negative space

For Java/Spring/testing/maintainability changes, check missing:
- boundary validation test;
- denied-path security test;
- transaction/persistence integration test;
- serialization/contract test;
- configuration binding/default test;
- behavior test for new domain rule;
- invariant/validator test for changed defensive contract;
- failure-path and retry test;
- stale-state/claim-lost/idempotency test for lock/reload or async duplicate flows;
- local cleanup needed to make the changed behavior reviewable.

## Findings to avoid

Do not report:
- formatter/linter issues;
- generic unit-test coverage requests;
- mock-vs-integration preference without risk;
- old code smell outside touched/adjacent area;
- framework concern already disproven by project tests/config;
- broad refactor request when a local observable fix is enough.
