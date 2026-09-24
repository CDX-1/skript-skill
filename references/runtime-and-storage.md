# Runtime and Storage

Use this reference for persistent state, memory or startup concerns, storage configuration, player data, histories, caches, and large variable trees.

## Use the correct mental model

Treat non-local Skript variables as an in-memory runtime data structure backed by configured persistent storage. At startup, saved variables are loaded into Skript's variable system; ordinary variable access should not be designed as if it were an on-demand SQL query. Changes also create persistence work through the configured storage implementation.

Exact loading, queuing, serialization, and flushing details can change across Skript versions and storage implementations. Verify implementation-specific claims against the target version. The stable engineering consequence is that choosing SQLite or MySQL does not make an unlimited Skript variable tree free, lazy, or suitable for arbitrary analytical queries.

The core variable subsystem supports storage implementations rather than changing the language-level state model into an ORM. Account for both sides of every persistent value:

- Runtime cost: memory, lookup, iteration, copying, and garbage pressure
- Persistence cost: serialization, queued changes, I/O, startup loading, and shutdown or flush behavior

## Classify state before storing it

Ask these questions:

1. Must this survive a restart?
2. Is it authoritative data, a cache, or a value that can be derived?
3. What creates an entry?
4. What bounds the number of entries per player and globally?
5. What removes stale entries, and when?
6. Is the access pattern point lookup, prefix iteration, sorting, aggregation, or history search?
7. How many writes can one user action generate?
8. What happens during reload, crash recovery, and partial feature failure?

Use local variables for request-, event-, and function-scoped computation. Persist only the minimum state required to restore behavior.

## Estimate growth

For per-player data, estimate at least:

```text
total entries ~= known players x fields per player
              + nested collection entries
              + retained historical entries
```

Use known players rather than concurrently online players when data is never deleted. Include inactive users, generated identifiers, and abandoned objects.

Small bounded values such as preferences, balances, cooldown deadlines, and feature flags are natural persistent-variable candidates. Treat the following as warning signs:

- Timestamp-keyed history with no retention policy
- A key for every block, entity, item, message, or transaction ever observed
- Duplicated indexes of the same underlying records
- Cached values that are never invalidated
- Frequent scans or sorts of an entire player namespace
- Repeated writes in movement, damage, packet, or tick-rate events
- Storing large serialized objects when a stable identifier would suffice

## Select storage intentionally

Use persistent Skript variables for bounded operational state. Consider another model when the feature needs large histories, range queries, cross-server coordination, reporting, transactions, or independent retention and backup policies.

Possible alternatives depend on the server and installed language surface:

- Recompute derived data when cheaper than storing it.
- Keep bounded ephemeral state in locals or an explicitly managed cache.
- Attach object-owned metadata through supported persistent-data-container syntax when lifecycle and ownership match.
- Use a purpose-built database integration for queryable datasets.
- Implement a plugin when strict consistency, large-scale data modeling, or specialized indexing is central to the feature.

Do not recommend an alternative solely because it sounds more sophisticated. Explain which access pattern or scale requirement justifies it.

## Design keys and cleanup

- Namespace data by feature.
- Prefer UUIDs for durable player identity.
- Keep key shape consistent so a subtree has one meaning.
- Avoid interpolating unconstrained user text into variable paths.
- Define cleanup for temporary records, expired cooldowns, removed entities, deleted worlds, inactive users, and uninstalled features.
- Be precise when deleting prefixes; a parent deletion may affect all descendants.

Cleanup itself can be expensive. Avoid moving a full-dataset scan into a frequent periodic event. Prefer deletion when the owning lifecycle event occurs, bounded batches, or storage designed for expiration when available.

## Durability claims

Do not promise that a change is synchronously durable merely because the variable changed in memory. If crash consistency, flush timing, multi-server visibility, or storage failure matters, inspect the target Skript version and backend implementation and test the failure mode. State any guarantees that remain unknown.
