# Skript Language Model

Use this reference when writing or reviewing ordinary Skript syntax. It is a reasoning guide, not a substitute for version-matched documentation.

## Think in registered syntax

Skript parses registered patterns into language elements. Natural-looking wording is valid only when a matching pattern is registered by core Skript or a loaded addon.

Classify code into these categories:

- **Events** establish execution context and expose event values.
- **Conditions** test state and do not perform an action.
- **Effects** perform an action.
- **Expressions** produce values, sometimes only in particular contexts.
- **Sections** own an indented block and may establish additional context.
- **Structures** define top-level constructs such as commands, functions, options, or variables.

When a line is uncertain, identify its category and search for the exact registered pattern. Do not repair it by trying increasingly plausible English phrases.

## Context is part of validity

An expression can exist in the language yet be invalid in the current event. Before using an event value such as `player`, `attacker`, `victim`, `projectile`, or `event-item`, confirm:

1. The event exposes that value.
2. Its possible types include what the next expression expects.
3. It can be absent in any event variant.
4. Its state is still meaningful after a delay.

Guard optional or broad types before narrowing them. Do not assume an attacker is a player, a command sender is a player, or an inventory click has an item.

Treat a delay as a lifecycle boundary. Capture required scalar identifiers and values into locals before waiting; afterward, revalidate mutable players, entities, inventories, worlds, and permissions as appropriate.

## Variables and collections

- `{_name}` is local temporary state. Function parameters and local list variables follow the same underscore convention.
- `{namespace::key}` is global persistent state unless the environment deliberately configures it otherwise.
- `{namespace::key::*}` addresses a list-variable subtree.
- A list variable may have meaningful keys. Preserve keys intentionally instead of assuming every list is sequential.
- Use `loop-index` when the key matters and `loop-value` for the value. Confirm nested-loop forms in the target documentation rather than guessing numbered variants.
- Deleting a broad prefix can remove an entire subtree. Construct and review deletion keys carefully.

Prefer stable identifiers such as player UUIDs for durable ownership. Use player objects or names as durable keys only when their serialization and rename behavior are explicitly desired and verified.

## Functions

Use functions to centralize repeated business rules, not to disguise side effects. Verify the target version's accepted parameter and return-type syntax. Keep inputs typed when supported and make failure behavior clear when a value may be absent.

Avoid hidden dependence on event values inside reusable functions. Pass required context explicitly. Keep persistent writes visible in the function's purpose or naming.

Keep the entire function signature and each function call on one physical line unless the target version explicitly supports multiline inputs. Inside the function, access a parameter such as `amount` through its local variable form `{_amount}`.

The return effect returns objects; it is not a bare control-flow statement. Do not write an empty `return` or return a value from a function with no declared return type. Use documented stop/exit control flow for an early end to a void function.

## Compound conditions

For several independent conditions, prefer the documented multiline `if all:` or `if any:` form with condition lines followed by an aligned `then:` section. Do not assume other languages' `&&`, `||`, `!`, or arbitrary inline boolean composition.

Do not interpret this as a global ban on the words `and` and `or`: an individual registered expression or list pattern can legitimately contain them. Distinguish a connective inside one documented pattern from an attempt to join separate conditions.

## Commands

Define and test:

- Player versus console behavior
- Permission and denial behavior
- Required and optional arguments
- Argument types and invalid-input feedback
- Tab completion only when supported by the chosen language surface
- Cooldown persistence and cleanup semantics

Do not treat user-controlled text as a trusted variable name, command, console instruction, or parsing pattern without deliberate validation.

## Ambiguity

Skript's converters and broad object types can make a line ambiguous even when every word exists. Resolve ambiguity by using documented forms, storing an intermediate value with an obvious type, checking the type, or rewriting the operation into smaller statements. Parentheses may help grouping but do not invent type information.

Prefer boring, explicit forms over compressed syntax when code depends on converter choice, event context, list keys, or addon interactions.
