# Data, Transactions, and Migrations

## Decision rules

Persistent data semantics and migration history outlive a single deployment; change them conservatively.

For data-affecting work, reason from:

```text
state/invariant -> consistency boundary -> query/schema/migration -> rollout/rollback impact -> proof
```

### Migration history

- Never modify a versioned migration that may already have reached a shared/downstream environment; add a new migration and roll forward.
- Edit or squash a local/unmerged migration only with clear evidence it has not been applied outside the current branch/workspace.
- When uncertain whether a migration escaped, treat it as immutable.

### Schema evolution

- Prefer changes compatible with old and new application versions during rolling deployment.
- Use staged expand/backfill/switch/contract evolution when an immediate breaking schema change would be unsafe.
- For new non-null data, define how existing rows become valid before enforcing the invariant.
- Add indexes/constraints deliberately; consider table size, lock behavior, write amplification, and deployment ordering.
- Keep semantically equivalent state/identity/value columns consistent in type, nullability, precision, representation, defaults, and constraints unless a domain reason differs.
- Verify database-engine-specific index, FK, cascade, locking, JSON, time-zone, and generated/default behavior instead of assuming portability.

### Transactions and data consistency

- Align a transaction boundary with the business invariant it must protect and keep it no broader than necessary.
- Treat propagation/isolation/locking changes as semantic changes, not tuning knobs.
- Avoid remote calls or unbounded work while holding a DB transaction unless the failure/consistency trade-off requires it.
- For DB update plus event/message publication, use an established atomic/reliable publication mechanism such as transactional outbox when loss or reordering would violate the required semantics.
- Consumers must tolerate duplicate delivery when the delivery model can produce duplicates; deduplication/inbox may be required for critical side effects.

## Failure modes

Watch for:

- editing an applied migration;
- destructive schema changes without staged rollout;
- adding `NOT NULL` without existing-row strategy;
- dropping data still read by old code;
- long/blocking migrations on hot tables without operational reasoning;
- missing access path for new query/relationship patterns;
- N+1 or unbounded reads introduced by repository changes;
- transactions spanning network calls;
- event publication that can diverge from committed state;
- cascades/retention behavior conflicting with aggregate/data ownership.

## Verification

Use migration validation and a production-like database when dialect, constraints, locking, transaction rollback/commit, indexes, or migrations matter. Verify risky migrations with representative data shape/query plans where practical, and prove DB/event consistency at the integration level when that invariant is part of the change.
