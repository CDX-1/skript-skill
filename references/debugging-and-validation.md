# Debugging and Validation

Use this reference to diagnose parser errors, runtime behavior, reload failures, regressions, migrations, and final verification.

## Collect the exact failure

Obtain or inspect:

- The full reload output, not only the first error line
- Script filename and referenced line numbers
- Surrounding indentation and enclosing event/section
- Skript, server, Minecraft, Java, and addon versions
- Startup warnings about addons or dependency failures
- Expected behavior and the smallest observed counterexample

Later parser errors may cascade from an earlier malformed line or indentation boundary. Start with the first root error, reload again, and then assess what remains.

## Classify the failure

- **Unknown syntax:** wrong wording, missing addon, incompatible version, or failed addon load
- **Cannot understand condition/effect:** wrong category, ambiguity, or invalid pattern
- **Invalid event value/context:** expression exists but not in this event or section
- **Type mismatch:** expression returns a broader or different type than the consumer accepts
- **Indentation/section failure:** malformed nesting or an expression placed outside its owning structure
- **Load-order failure:** cross-script function, custom syntax, or dependency is unavailable when parsed
- **Runtime logic failure:** code parsed but state, ordering, or guards are wrong
- **Lifecycle failure:** reload, reconnect, restart, death, unload, or deletion exposes stale/missing state
- **Scale failure:** behavior is correct at small size but work or storage grows without a useful bound

## Reduce before rewriting

Create a minimal reproduction containing the relevant trigger, inputs, and one disputed construct. Preserve the same versions and addon set. A successful minimal parse isolates syntax; it does not prove the full feature's ordering, persistence, or concurrency behavior.

When fixing existing code, make the smallest correction that explains the failure. Do not opportunistically migrate unrelated syntax or replace addons unless requested.

## Static review

When no compatible server is available, check:

- Every non-core construct has a known owner.
- Syntax is supported by the target versions.
- Indentation and section ownership are consistent.
- Function signatures/calls are not split across physical lines unless supported by the target version.
- Compound independent conditions use documented conditional sections rather than imported boolean operators.
- No function uses a bare `return` or returns a value without a compatible declared return type.
- Event values exist and have compatible possible types.
- Optional values and non-player senders are guarded.
- Locals and persistent globals are intentionally distinguished.
- List keys and nested loops refer to the intended collection.
- Delayed or asynchronous work captures and revalidates state.
- Persistent data is bounded and has cleanup.
- Hot events do not perform broad scans or uncontrolled writes.
- Reload/load order and existing-online-player behavior are considered.

Report this as static review, not a successful parse.

## Runtime validation

Prefer a disposable server matching the target environment. Back up meaningful production data and avoid testing uncertain scripts on a live server.

1. Start the server and confirm Skript plus all required addons load cleanly.
2. Reload only the target script using the command supported by that installation.
3. Inspect the entire console output for errors and warnings.
4. Exercise success, denial, empty, missing, console, and wrong-type paths as relevant.
5. Test multiple players or entities when state can leak between owners.
6. Test waits, disconnects, death, world changes, and removed entities when relevant.
7. Reload the script and retest.
8. Restart the server when persistence is part of the feature.
9. Inspect variable growth, timings, or profiler output for scale-sensitive work.

Do not treat “0 parse errors” as proof of correct behavior.

## Migrations

For version or addon migrations:

- Inventory syntax by owner before editing.
- Read release and migration notes across the actual version gap.
- Separate mechanical syntax changes from semantic changes.
- Preserve or explicitly migrate persistent variable keys and serialized types.
- Test old data, not only a fresh installation.
- Keep a rollback path for storage-format or schema changes.

## Explain the outcome

State which layer failed, why the correction addresses it, which versions were verified, and what remains untested. Include exact reload and behavior checks the user can repeat.
