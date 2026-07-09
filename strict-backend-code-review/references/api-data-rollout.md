# API, Data, and Rollout Review Reference

Load this file only when the changed surface touches REST/gRPC APIs, DTOs, request/response schemas, event/message contracts, persistence, transactions, migrations, indexes, constraints, serialization, configuration compatibility, versioning, rollout, or rollback.

For SQL/DDL-specific review, also load `db-migrations-playbook.md`.
For OpenAPI/protobuf/generated-contract review, also load `openapi-contract-playbook.md`.

## Review focus

Check whether the PR preserves:
- external contract compatibility;
- old/new application version compatibility;
- producer/consumer compatibility;
- schema and serialization semantics;
- transaction correctness;
- data integrity;
- migration safety;
- rollback safety;
- operational deployability.

## API contracts

Report when the PR:
- removes or renames a public field, enum value, status, endpoint, parameter, or error shape without compatibility plan;
- changes null/missing/default semantics without contract tests;
- changes response filtering or includes internal/sensitive fields;
- changes validation behavior without documenting observable error semantics;
- changes idempotency semantics of POST/PUT/PATCH/DELETE;
- changes pagination, sorting, filtering, or time-zone semantics;
- returns persistence entities from API boundaries;
- changes generated OpenAPI/schema output unintentionally;
- changes API behavior through code but not contract/schema/docs when those are part of the project workflow.

Required change should preserve compatibility, update the contract, or define a versioned/deprecated transition.

Names that are part of generated/public contracts are not style-only. Review them for compatibility, reference stability, generated-client behavior, and tooling behavior.

## Message and event contracts

Report when the PR:
- removes, renames, or changes type/meaning of event fields without compatibility plan;
- introduces required fields without old producer/new consumer compatibility;
- changes event ordering, deduplication key, partition key, correlation key, or aggregate identifier;
- emits events before durable state commit;
- emits events that expose internal-only domain state;
- changes producer behavior without updating consumer expectations/tests;
- changes consumer assumptions without checking existing producers;
- treats at-least-once delivery as exactly-once without durable idempotency.

Check old producer -> new consumer and new producer -> old consumer scenarios.

## Serialization and DTO semantics

Report when the PR:
- conflates missing, null, empty, and default values where they have different business meaning;
- changes Jackson naming, inclusion, enum, date/time, BigDecimal, or polymorphic behavior without contract evidence;
- accepts unknown fields in unsafe flows or rejects them in compatibility-sensitive flows without intent;
- changes validation annotations in a way that alters API behavior;
- maps DTOs to entities in a way that overwrites fields not present in the request;
- exposes server-controlled fields as client-writable.

For PATCH/partial updates, require explicit tests for missing, explicit null, valid value, and unauthorized field update when relevant.

## Data model and persistence

Report when the PR:
- creates a schema that application code cannot safely read/write during rollout;
- adds NOT NULL/unique/foreign key constraints without backfill/cleanup sequencing;
- changes relationships without verifying supporting constraints, access paths, or documented local convention;
- drops or renames columns/tables before all code paths stop using them;
- changes indexes without considering query shape, uniqueness, locking, referential actions, and rollout cost;
- changes semantically equivalent schema elements in a way that makes type, nullability, default, precision, collation, or representation semantics inconsistent;
- changes entity relationships in a way that can cause unintended cascade, orphan removal, or N+1 queries;
- changes equality/hashCode/toString on JPA entities in a way that can break persistence behavior or leak data;
- relies on application-only uniqueness without database constraint for critical invariants;
- persists invalid state that domain rules should reject.

Prefer expand/contract migrations for compatibility-sensitive changes.

## Transactions

Report when the PR:
- splits state changes that must be atomic;
- wraps too much work in one transaction, especially remote calls or slow operations;
- performs external side effects inside a transaction without idempotency/compensation reasoning;
- reads outside the transaction then writes based on stale state in a concurrent path;
- changes isolation/propagation semantics without evidence;
- uses transaction annotations where proxies will not apply;
- catches exceptions in a way that prevents required rollback;
- commits state before validation, authorization, or invariant checks complete.

Required change should identify the transactional boundary and the state transition that must be atomic.

## Migration safety

Check:
- expand before use;
- backfill before constraint;
- dual-write/dual-read only when needed and bounded;
- old app -> new schema;
- new app -> transition schema;
- rollback after partial rollout;
- repeatability/idempotency of migration scripts;
- lock duration and table size risk;
- data cleanup before destructive changes;
- migration observability and failure recovery.

Report missing migration evidence when it affects rollout safety.

## Rollout and rollback

Report when the PR:
- requires all pods to update simultaneously;
- breaks rolling deployment with mixed old/new pods;
- changes config defaults in a way that old pods cannot tolerate;
- changes DB/schema/API/event compatibility without deployment order;
- removes old behavior before consumers/producers/clients are migrated;
- adds feature flags without safe default or cleanup boundary;
- cannot be rolled back after migration or external contract change;
- needs operational sequencing but does not document it.

Use Rollout / Compatibility as category when old/new version safety is the main merge risk.

## External clients and consumers

Report when the PR:
- changes externally observed behavior without considering clients;
- changes error codes/status mapping in client-visible way;
- changes retryability semantics;
- changes timeout, rate, pagination, or payload size assumptions;
- changes auth scopes/permissions required by clients;
- changes contract tests or generated client compatibility without evidence.

## Negative space

For API/data/rollout changes, check missing:
- OpenAPI/protobuf/event schema update;
- contract tests;
- migration/backfill script;
- rollback plan;
- feature flag/default config;
- Helm/env/config wiring;
- old/new version compatibility tests;
- denied/invalid input tests;
- data repair or cleanup step;
- observability for migration or rollout failure.

## Findings to avoid

Do not report:
- hypothetical compatibility risk with no changed contract or consumer path;
- database optimization preference without query/scale evidence, except when the index or constraint affects integrity, uniqueness, locking, referential actions, documented query paths, or rollout safety;
- broad migration redesign when a local sequencing fix is enough;
- style-only DTO or entity naming issues; generated/public contract names are not style-only when they affect compatibility or tooling;
- unrelated legacy schema problems not touched or amplified by the PR.
