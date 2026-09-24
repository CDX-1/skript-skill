# Addon Ecosystem

Use this reference whenever existing code or the requested solution involves an addon, unfamiliar syntax, Java interop, custom types, packets, NBT/PDC, GUIs, databases, proxies, or other non-core behavior.

## Treat addons as language extensions

An addon can register new events, effects, conditions, expressions, sections, structures, types, converters, and event values. This can change what parses, which interpretation wins, and what values can flow into other syntax.

Consequences:

- English that works on one server may fail on another with a different addon set.
- Similar features from different addons are not interchangeable.
- A syntax entry without its owning addon and version is incomplete evidence.
- Parser conflicts, converter chains, registration order, and optional integrations can create surprising behavior.
- Addon syntax can compile but still be unsafe in the current thread, event, or platform.

Never attribute addon syntax to core Skript. Never silently add a dependency just to make guessed syntax valid.

## Build an environment inventory

Inspect plugin lists, dependency files, server logs, existing script headers/comments, and adjacent syntax. Record:

```text
Server implementation and version:
Skript version:
Addon and version:
Feature or syntax supplied:
Required companion plugin/API:
Evidence source:
```

The installed plugin jar or startup log is stronger evidence of the actual environment than a generic tutorial. A plugin name without its version is insufficient for migration-sensitive code.

## Establish provenance per construct

For each non-core construct that matters, determine:

- Owning addon
- Exact documented pattern
- Addon version in which it exists
- Compatible Skript, Java, Minecraft, and server versions when documented
- Required event or thread context
- Return type and whether it may be absent
- Whether the syntax is deprecated, experimental, or replaced

Keep this analysis internally for ordinary work; surface it as a dependency list and compatibility notes rather than overwhelming the user.

## Avoid cross-addon synthesis

Do not combine the wording of one addon's effect with another addon's expression. Watch especially for ecosystems with overlapping functionality such as NBT, GUIs, packets, reflection, databases, scoreboards, holograms, and proxies.

When existing code mixes addons:

1. Identify which lines belong to which addon.
2. Confirm that the installed versions can coexist.
3. Preserve established syntax unless the task calls for migration.
4. If consolidating dependencies, compare behavior and data compatibility before rewriting.

## Interop and threading

Reflection, direct Java access, packet APIs, database clients, and asynchronous sections weaken the simple guarantees of ordinary Skript. For such code, verify:

- Whether Bukkit/Paper objects are accessed only from permitted threads
- Whether callbacks return to the main server thread before world/entity mutation
- Whether reflected members exist in the target server mapping/version
- Whether values crossing the boundary retain valid types and lifetimes
- Whether exceptions and failed futures/queries are handled

Do not introduce reflection merely to avoid researching a supported core or addon expression.

## Dependency behavior

If an addon is required, make that visible in the result. If the user requested core-only Skript, do not solve the request with an addon. If the requested behavior cannot be implemented reliably under the stated constraint, explain the limitation and offer separately labeled alternatives.
