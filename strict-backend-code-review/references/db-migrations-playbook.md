# DB and Migrations Playbook

Load this file when the changed surface touches SQL, DDL, migrations, Liquibase/Flyway, table/index/constraint definitions, persistence schema, or migration rollout/rollback behavior.

## Goal

Review the resulting database schema across changed files, not only the local diff.

Treat SQL and migrations as first-class code. Do not reduce this pass to Java persistence context.

## Required workflow

1. Identify created, altered, split, moved, or removed tables, columns, indexes, constraints, relationships, and referential actions.
2. Build a schema-risk inventory.
3. Build a referential-constraint inventory when relationships are added, moved, recreated, or materially depended on.
4. Build a schema consistency table for semantically equivalent schema elements.
5. Check migration order, mixed-version deploy safety, rollback safety, and lock/implicit-action behavior.
6. Report only findings with concrete schema evidence tied to changed surface.

## Required artifacts

Build these artifacts internally. Print them only in audit mode or when needed as evidence for a finding/question.

### Schema-risk inventory

```text
object -> change type -> old invariant -> new invariant -> risk note
```

Use for tables, columns, indexes, constraints, defaults, relationships, and referential actions.

### Referential-constraint inventory

```text
referencing object/column(s) -> referenced object/column(s) -> supporting access path/constraint coverage -> referential action -> notes
```

Use this to check whether relationship enforcement, lookup paths, locking behavior, and implicit actions are supported by the resulting schema and local database behavior.

### Schema consistency table

```text
semantic element family -> object/column -> type -> nullability -> default -> precision/representation semantics -> notes
```

Use this for schema elements that have the same semantic role across tables, modules, or contract boundaries.

## Checks

Report when the PR:
- creates semantically equivalent schema elements with inconsistent type, nullability, default, precision, collation, representation semantics, or behavior-changing naming;
- adds, moves, or recreates relationships without verifying the supporting physical access path, constraint coverage, or documented local convention;
- assumes database-specific constraint/index behavior without checking the actual engine or project migration conventions;
- drops or fails to preserve constraints or indexes that represented business invariants in the replaced or split schema;
- adds NOT NULL, UNIQUE, relationship, CHECK, or default constraints without safe sequencing for existing data;
- introduces referential actions that conflict with application ownership semantics, retention rules, or business rules;
- makes implicit database actions affect retained or externally visible data without explicit justification;
- creates a migration that is unsafe for old app -> new schema, new app -> transition schema, partial rollout, rollback, or repeated application;
- performs potentially blocking DDL on large or hot tables without lock-duration reasoning when such table scale is part of the changed surface or local context;
- changes schema elements used as identifiers, state markers, routing keys, or compatibility boundaries without checking readers, writers, contracts, and data repair needs.

## Evidence requirements

For relationship/index findings, name:
- referencing and referenced objects/columns;
- existing supporting access path or constraint status;
- realistic query, write, referential action, or locking path.

For schema consistency findings, name:
- semantic element family;
- affected objects/columns;
- concrete incompatible type/default/nullability/representation semantics;
- likely read/write, migration, or compatibility consequence.

For implicit-action findings, name:
- relationship or schema object involved;
- data or state affected implicitly;
- why the application, retention policy, cache, versioning, or domain model would not observe it safely.

## Tests and verification

Prefer migration/integration verification through the real database engine when practical:
- migration applies from an empty database;
- migration applies from representative existing data;
- metadata assertions for critical indexes, constraints, defaults, and column types;
- old/new app compatibility smoke when rollout is relevant;
- rollback or forward-fix evidence when rollback is not possible.

## Findings to avoid

Do not report:
- generic SQL formatting or naming preferences;
- index micro-optimizations without changed query, constraint, locking, referential action, uniqueness, or rollout path;
- unrelated legacy schema issues not touched, depended on, exposed, or materially amplified by the PR;
- broad migration redesign when a local schema or sequencing fix is enough;
- speculative scale concerns when table size/query path is neither changed nor locally evidenced.
