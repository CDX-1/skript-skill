# Architecture and Performance

Use this reference for production features, performance reviews, recurring events, large collections, persistent systems, or scripts expected to serve many players.

## Budget work by frequency and scale

Estimate cost as:

```text
cost per operation x invocations per second x objects or entries visited
```

A small loop inside a rare administrative command differs from the same loop inside movement, damage, inventory, packet, or tick-based events. Review nested loops multiplicatively.

Hot-path warning signs include:

- Looping all players, entities, blocks, worlds, or persistent records
- Sorting a complete leaderboard on every display or update
- Repeatedly parsing text, locations, dates, or serialized objects
- Rebuilding GUIs every tick
- Performing storage or network operations on the main thread
- Many persistent-variable mutations for one high-frequency event
- Periodic polling for a condition that an event can signal

Prefer event-driven updates, incremental aggregates, bounded batches, direct keyed lookup, and cached results with explicit invalidation.

## Model feature state

For each state group, document:

- Owner: player, entity, world, match, or feature
- Lifetime: statement, event, session, round, restart-persistent, or historical
- Maximum cardinality
- Read/write frequency
- Cleanup trigger
- Recovery behavior after reload or restart

Use locals for transient calculations. Use bounded persistent variables for restart-persistent operational state. Use PDC when data naturally belongs to a supported Bukkit object and should share its lifecycle, after verifying version and syntax support. Use an external database when query patterns and scale justify it.

## Avoid accidental global work

- Index data by the key used for normal lookup.
- Do not scan every stored player to answer a single-player question.
- Maintain leaderboard or aggregate state when updates are less frequent than reads, but define invalidation and rebuild behavior.
- Bound histories by count or time and make pruning incremental.
- Avoid wildcard deletion or copying of huge namespaces in a live hot path.
- Do not create one periodic task per player when one bounded scheduler or lifecycle event suffices.

## Respect server-thread rules

World, entity, inventory, and most Bukkit/Paper mutations normally belong on the server's allowed execution context. An addon's asynchronous syntax does not make arbitrary server API use safe. On Folia, ownership and scheduler rules differ further; verify support in Skript and every involved addon instead of assuming Paper behavior.

Use asynchronous work only for operations that permit it, such as suitably designed external I/O or pure computation. Return to the correct scheduler/context before mutating server state.

## Design for reloads and lifecycle changes

Skript scripts can be reloaded independently. Account for:

- Re-registering or duplicating scheduled work
- Temporary state that disappears on reload
- Persistent state whose schema changed
- Players already online when the script loads
- Entities or worlds that disappeared
- Cleanup when the script unloads
- Functions or custom syntax supplied by a script that loads later

Do not use an `on load` repair that rescans or rewrites an unbounded dataset without considering startup impact and interruption safety.

## Optimize from evidence

Correctness and clear state boundaries come first. When performance is a concern, gather timings, event frequency, entry counts, heap behavior, and profiler evidence where possible. Do not claim an optimization from fewer source lines or stylistic compression.
