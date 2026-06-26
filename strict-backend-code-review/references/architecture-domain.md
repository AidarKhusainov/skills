# Architecture and Domain Review Reference

Load this file only when the changed surface touches architecture, module boundaries, dependency direction, DDD, domain model, use cases, aggregates, domain services, policies, commands, events, or architecture documentation.

## Review focus

Check whether the PR preserves:
- explicit architectural boundaries;
- dependency direction;
- bounded context ownership;
- domain language consistency;
- aggregate invariants;
- application-service orchestration boundaries;
- infrastructure isolation;
- behavior consistency across entry points;
- local simplicity in touched code.

## Architecture boundaries

Report when the PR:
- moves business decisions into controllers, adapters, repositories, mappers, jobs, or configuration classes;
- introduces dependency from domain/application code to infrastructure or transport code;
- bypasses the application/use-case layer from one entry point while other entry points use it;
- creates duplicate orchestration paths for the same use case;
- introduces a new architectural convention without ADR/design/migration evidence;
- changes transaction, authorization, or persistence ownership without architecture evidence;
- mixes command handling, domain decisions, persistence, and transport mapping in one class or method;
- adds a second competing pattern for the same module responsibility.

Do not report architecture preference if the local convention is documented, recent, safe, and intentional.

## Bounded contexts and module ownership

Report when the PR:
- reads or mutates another bounded context's internal state directly;
- imports internal classes across module boundaries;
- duplicates another context's domain rules instead of using an explicit contract;
- changes shared DTOs/entities in a way that leaks one context's model into another;
- uses database tables as an integration contract between contexts without explicit ownership;
- adds cross-context calls without failure, authorization, and compatibility reasoning.

Required change should restore ownership through an existing public API, application port, event contract, anti-corruption layer, or documented boundary.

## Domain model and invariants

Report when the PR:
- allows invalid aggregate/entity state to be constructed or persisted;
- spreads one business rule across multiple services/controllers/consumers;
- duplicates domain decisions that already exist in a policy/specification/domain service;
- performs state transitions without checking allowed previous states;
- updates related domain state outside the aggregate or invariant owner;
- relies on UI/client validation for server-side domain invariants;
- treats domain errors as generic technical failures;
- introduces primitive obsession that hides important domain meaning in risky code.

Prefer behavior-oriented tests around public use-case boundaries for domain rules.

## Aggregates and consistency

Report when the PR:
- updates multiple aggregate roots in one synchronous transaction without explicit consistency reason;
- introduces immediate consistency across contexts where eventual consistency is the established pattern;
- publishes domain events before durable state is committed;
- emits integration events that can describe a state not committed in the database;
- exposes mutable aggregate internals to callers;
- lets repositories persist partially validated domain objects;
- couples aggregate lifecycle to infrastructure-specific concerns.

If the risk is distributed consistency, classify primarily as Data, Rollout / Compatibility, Resilience, or Concurrency when those are the main merge risk.

## Use cases and application services

Report when the PR:
- puts use-case orchestration in controller, listener, scheduler, mapper, or repository code;
- skips authorization, validation, domain decision, transaction, or event publication path used by equivalent entry points;
- handles the same command differently through HTTP, messaging, scheduled job, or internal API;
- introduces command handlers with unclear ownership or duplicate naming;
- returns persistence entities or infrastructure objects from application boundaries;
- makes application services own domain decisions that should belong to domain policy/aggregate.

Required change should name the intended owner and the observable behavior that must be preserved.

## Domain language and naming

Report when naming hides merge-relevant meaning:
- method name says `validate` but mutates or persists;
- `process`, `handle`, `execute`, `status`, `type`, `data`, `result`, or boolean names hide domain semantics in risky code;
- test names do not match asserted behavior;
- DTO/domain names imply different lifecycle, ownership, or state than implementation;
- new terminology conflicts with established ubiquitous language.

Do not report cosmetic naming if it does not affect understanding, correctness, testability, or future safe modification.

## Boy Scout cleanup

Require cleanup when:
- the PR changes the problematic code path;
- the existing structure makes the new behavior hard to verify;
- duplication or nesting increases risk of incorrect future changes;
- local extraction or renaming would make the changed rule testable;
- cleanup is small enough to stay within the PR's coherent purpose.

Do not demand broad legacy refactoring outside touched or directly adjacent code.

## Negative space

For architecture/domain changes, check missing:
- ADR/design note for new conventions;
- module README update for changed boundaries;
- tests through every public entry point affected by a domain rule;
- migration/deprecation plan for replaced patterns;
- contract update when domain state is exposed externally;
- authorization/audit update for security-sensitive domain actions.

## Findings to avoid

Do not report:
- generic Clean Architecture or DDD preference without local evidence;
- style-only package/class naming issues;
- old architecture problems not touched or amplified by the PR;
- broad rewrite requests when a local root-cause fix is sufficient;
- disagreement with documented project convention unless it creates concrete risk.
