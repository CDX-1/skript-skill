---
name: skript-development
description: Write, review, debug, optimize, and modernize Minecraft Skript code for Paper or Spigot with version-aware core syntax, addon provenance, runtime storage knowledge, and reload-based validation. Use for .sk files, Skript architecture, parser errors, performance problems, migrations, or addon compatibility; do not use for Java/Kotlin plugins or unrelated scripting languages.
---

# Skript Development

Produce Skript that fits the user's actual server, parses with the installed language surface, and remains understandable and operable as its data grows. Treat Skript as a runtime with persistence and third-party language extensions, not merely English-like syntax.

## Establish the environment

Determine the following when they affect the result:

- Minecraft version and server implementation, including Folia if applicable
- Skript version
- Installed addons and their versions
- Whether existing code or data must remain compatible
- Expected scale: players, entities, stored records, and event frequency

Use information already present in the project before asking the user. If details remain unavailable, make the smallest conservative assumptions, state them, and avoid version-sensitive or addon syntax that cannot be supported.

## Route the task

Read only the references needed for the request:

- Before generating or correcting Skript code, read [references/common-syntax-traps.md](references/common-syntax-traps.md) to avoid language-shaped mistakes commonly imported from JavaScript, Python, Java, and other languages.
- For syntax, variables, functions, commands, events, and event values, read [references/language-model.md](references/language-model.md).
- For persistent variables, storage backends, memory use, or large per-player datasets, read [references/runtime-and-storage.md](references/runtime-and-storage.md).
- When any addon is installed, requested, suspected, or used by existing code, read [references/addon-ecosystem.md](references/addon-ecosystem.md).
- When exact syntax, ownership, version support, or deprecation is uncertain, read [references/documentation-research.md](references/documentation-research.md).
- For production design, recurring events, large loops, concurrency, or state modeling, read [references/architecture-and-performance.md](references/architecture-and-performance.md).
- For broken code, migrations, or final verification, read [references/debugging-and-validation.md](references/debugging-and-validation.md).

## Work from evidence

Classify each non-trivial construct as core Skript or addon-provided. Verify unfamiliar, version-sensitive, or addon syntax against documentation matching the target versions. Never invent syntax because it sounds like plausible English, silently require an addon, or combine similar syntax from different addons.

Prefer current core Skript when it expresses the requirement cleanly. Preserve an existing addon choice when the user needs compatibility or asks for it. Do not rewrite a working dependency merely to impose a preference.

## Design before coding

For substantial features, identify:

- Entry points: events, commands, functions, and load/unload behavior
- State ownership, lifetime, persistence, bounds, and cleanup
- Event-context values and what happens across waits or asynchronous work
- Hot paths and the approximate amount of work per invocation
- Core and addon dependencies
- Reload, restart, reconnect, death, and entity-lifecycle behavior

Use local variables for temporary computation. Use namespaced, UUID-keyed persistent variables for bounded player state. Do not use persistent variable trees as an unbounded event log or assume a SQL storage backend makes them lazy database queries.

## Write complete, explicit code

- Keep indentation consistent and preserve the repository's established style.
- Keep function declarations, parameter lists, and function calls on one physical line unless the target Skript version explicitly documents multiline support.
- Use Skript's multiline conditional sections for several independent conditions; do not invent general-purpose `&&`, `||`, or language-style boolean chaining.
- Do not emit a bare `return`. Return a value only from a value-returning function; use documented stop/exit control flow when a void function must end early.
- Use clear namespaces for global variables and functions.
- Guard player-only, entity-specific, and optional values before using them.
- Capture values needed after a delay and revalidate objects whose state may change.
- Keep permissions, command senders, arguments, failure messages, and cleanup behavior explicit.
- Comment decisions and invariants, not obvious syntax.

When modifying an existing project, inspect nearby scripts and configuration first. Make focused edits and preserve unrelated behavior.

## Validate proportionally

The meaningful syntax check is loading the script with the target Skript, addons, and server version. When a compatible test server is available, reload the individual script and inspect the complete console output. Exercise the relevant runtime and lifecycle cases after it parses.

If runtime validation is unavailable, perform the static review in the debugging reference and say that the code was not loaded by Skript. Do not describe visual inspection or a generic linter as proof that the script parses.

## Present the result

For generated code, provide:

1. Important environment assumptions
2. Complete code or a focused patch, according to the request
3. Required addons and minimum versions when verified
4. Reload and behavior tests
5. Any syntax or compatibility that remains unverified

For debugging, explain the cause, the smallest sound correction, and how to reproduce the verification. Distinguish parse failures, load-order or dependency failures, runtime logic errors, and scale-related performance problems.
