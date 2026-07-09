# DB and Migrations Playbook

Load this file when the changed surface touches SQL, DDL, migrations, Liquibase/Flyway, table/index/constraint/FK definitions, persistence schema, or migration rollout/rollback behavior.

## Goal

Review the resulting database schema across changed files, not only the local diff.

Treat SQL and migrations as first-class code. Do not reduce this pass to Java persistence context.

## Required workflow

1. Identify created, altered, split, moved, or removed tables, columns, indexes, constraints, FKs, and referential actions.
2. Build a schema-risk inventory.
3. Build an FK inventory table when FKs are added, moved, recreated, or materially depended on.
4. Build a schema consistency table for semantically identical columns.
5. Check migration order, mixed-version deploy safety, rollback safety, and lock/cascade behavior.
6. Report only findings with concrete schema evidence tied to changed surface.

## Required artifacts

Build these artifacts internally. Print them only in audit mode or when needed as evidence for a finding/question.

### Schema-risk inventory

```text
object -> change type -> old invariant -> new invariant -> risk note
```

Use for tables, columns, indexes, unique constraints, check constraints, FKs, defaults, and referential actions.

### FK inventory table

```text
child table.column(s) -> parent table.column(s) -> supporting index/unique coverage -> ON DELETE/ON UPDATE -> notes
```

A supporting index must cover the child-side FK columns in a useful leading-prefix order. A UNIQUE constraint can count only when it provides equivalent lookup/locking support for the child-side FK columns.

### Schema consistency table

```text
semantic column family -> table.column -> type -> nullability -> default -> time/precision semantics -> notes
```

Use this for audit columns, history timestamps, codes, identifiers, tenant/account keys, status fields, and equivalent domain columns split across tables.

## Checks

Report when the PR:
- creates semantically identical columns with inconsistent type, nullability, default, precision, collation, timezone semantics, or naming that changes behavior;
- makes audit/history columns (`created_at`, `updated_at`, `deleted_at`, event/history timestamps) inconsistent across related tables;
- adds, moves, or recreates FK child columns without supporting child-side index coverage, unless a local convention or schema evidence proves it is intentionally unnecessary;
- relies on PostgreSQL FK behavior as if child-side indexes were created automatically;
- drops or fails to preserve unique constraints/indexes that represented business invariants in the replaced/split schema;
- adds NOT NULL, UNIQUE, FK, CHECK, or default constraints without safe backfill/cleanup sequencing for existing data;
- introduces `ON DELETE` / `ON UPDATE` actions that conflict with project conventions, JPA ownership semantics, audit/history retention, or application-level business rules;
- cascade-deletes history/audit data without explicit retention justification;
- creates a migration that is unsafe for old app -> new schema, new app -> transition schema, partial rollout, rollback, or repeated application;
- performs potentially blocking DDL on large or hot tables without online/lock-duration reasoning when such table scale is part of the changed surface or local context;
- changes identifier, status, enum-like, or code columns without checking readers, writers, generated contracts, and data repair needs.

## Evidence requirements

For FK/index findings, name:
- child table and column(s);
- parent table and column(s);
- existing supporting index/constraint status;
- realistic query, parent DELETE/UPDATE, cascade, or locking path.

For schema consistency findings, name:
- semantic column family;
- affected table/column pairs;
- concrete incompatible type/default/nullability/time semantics;
- likely read/write, audit, migration, or compatibility consequence.

For cascade findings, name:
- FK constraint or table pair;
- data that would be deleted/updated implicitly;
- why the application, audit, history, cache, versioning, or domain model would not observe it safely.

## Tests and verification

Prefer migration/integration verification through the real database engine when practical:
- migration applies from an empty database;
- migration applies from representative existing data;
- metadata assertions for critical indexes, unique constraints, FKs, defaults, and column types;
- old/new app compatibility smoke when rollout is relevant;
- rollback or forward-fix evidence when rollback is not possible.

## Findings to avoid

Do not report:
- generic SQL formatting or naming preferences;
- index micro-optimizations without changed query, FK, locking, cascade, uniqueness, or rollout path;
- unrelated legacy schema issues not touched, depended on, exposed, or materially amplified by the PR;
- broad migration redesign when a local schema or sequencing fix is enough;
- speculative scale concerns when table size/query path is neither changed nor locally evidenced.
