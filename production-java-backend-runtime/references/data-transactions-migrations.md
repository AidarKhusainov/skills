# Data, Transactions, and Migrations

## Load this when

Load this reference for database schema, migrations, queries, repositories, transactions, indexes, constraints, backfills, outbox/inbox, event publication, or data consistency.

## Non-negotiable checks

Treat these as production contracts:

- Database schema.
- Data semantics.
- Migration history.
- Indexes and constraints.
- Transaction boundaries.
- Event/message publication tied to data changes.
- Backfill behavior.
- Repository query semantics.

Use environment-aware migration discipline:

- If a versioned migration may have reached any shared or downstream environment, do not edit it; create a new migration and roll forward.
- Local/unmerged migrations may be edited or squashed only when there is clear evidence they have not been applied outside the current branch.
- When uncertain, assume the migration is immutable.

## Data change map

For data-affecting changes, build this internal map:

```text
state/invariant -> transaction boundary -> query/schema/migration -> rollout/rollback impact -> verification
```

Use it to keep code, migration, transaction boundaries, data semantics, and tests aligned.

Print the map only when it clarifies a migration/data-safety decision or final residual-risk note.

## Preferred approach

For schema evolution:

- Prefer backward-compatible changes.
- Use expand -> backfill -> switch reads/writes -> contract for breaking or rolling-deploy-sensitive changes.
- Keep old and new code compatible during rolling deployments.
- Add indexes and constraints deliberately.
- Consider lock time, table size, write load, and rollout order.
- Include verification queries for risky changes.

For relationship changes:

- Verify database-engine-specific relationship/index behavior.
- Do not assume supporting child-side access paths exist implicitly.
- Verify cascade and referential actions against application ownership and retention semantics.

For schema consistency:

- Keep semantically equivalent columns and state markers consistent in type, nullability, default, precision, representation semantics, and constraints across related tables.

For transactions:

- Keep transaction boundaries explicit and small.
- Align transaction boundaries with business invariants.
- Do not add or change `@Transactional` casually.
- Justify propagation/isolation changes.
- Avoid remote calls while holding DB transactions unless explicitly justified.
- Avoid long-running work inside transactions.

For DB update + event/message publication:

- Consider transactional outbox or equivalent reliability pattern.
- Design consumers as idempotent when duplicate delivery is possible.
- Consider inbox/deduplication for critical consumers.

## Red flags

- Editing applied migration.
- Destructive migration without staged rollout.
- Non-null column added without default/backfill strategy.
- Dropping column still used by old code.
- Index added on large table without operational impact review.
- Business logic hidden in SQL without tests.
- N+1 query introduced.
- Transaction wraps remote call.
- Event published outside reliable transaction boundary.
- Consumer cannot handle duplicate messages.

## Verification

Prefer:

- Migration validation.
- Repository/query tests.
- Integration tests with production-like database.
- Transaction behavior tests for rollback/commit.
- Outbox/inbox tests for DB + event consistency.
- Backward compatibility tests for rolling deployment-sensitive changes.

## Final response notes

Mention:

- Migration strategy.
- Whether migration may be edited or must roll forward.
- Transaction boundary changes.
- Index/constraint/backfill risk.
- Data consistency assumptions.
