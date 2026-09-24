# Common AI Syntax Traps

Read this reference before generating or correcting Skript code. These errors are common because a model imports grammar from Python, JavaScript, Java, Kotlin, or another language into Skript's line-oriented registered syntax.

The rules below describe standard Skript. An addon or future release may add another form, but use it only when documentation for the target environment confirms it.

## Keep function syntax on one physical line

Standard function declarations place the name, complete parameter list, optional return type, and terminating colon on one line:

```skript
function award_points(player: player, amount: number, reason: text) :: number:
	# body
```

Do not format a declaration like a JavaScript or Python signature:

```skript
# Invalid in standard Skript
function award_points(
	player: player,
	amount: number,
	reason: text
) :: number:
```

Keep a function call and all of its arguments on one physical line as well:

```skript
set {_total} to award_points(player, 5, "daily reward")
```

Do not split a call after `(` or between arguments. If a call becomes unreadably long, calculate complex arguments in local variables first, shorten names without making them cryptic, or extract a smaller function.

This is version-sensitive. Multiline function inputs are the subject of upstream enhancement issue [#7924](https://github.com/SkriptLang/Skript/issues/7924); do not assume a proposed or in-review feature exists in the user's installed release.

## Do not import general boolean operators

Do not assume `&&`, `||`, `!`, or general language-style `condition and condition` / `condition or condition` composition is accepted. For several independent conditions, use the multiline conditional sections registered by Skript.

All conditions must pass:

```skript
if all:
	player has permission "rewards.claim"
	{rewards::claimed::%uuid of player%} is not set
	player's inventory has space for 1 diamond
then:
	give 1 diamond to player
```

At least one condition must pass:

```skript
if any:
	player has permission "staff.admin"
	player has permission "staff.moderator"
then:
	send "Staff access granted." to player
```

The condition lines belong under `if all:` or `if any:`. The effects belong under a sibling `then:` section aligned with the `if` section. Do not put effects among the condition lines.

Use nested ordinary `if` sections when their branching, failure handling, or short-circuit order needs to be visible. Use intermediate booleans only when they represent meaningful reusable state, not as a reflexive workaround.

### `and` and `or` are not globally banned words

Skript patterns may use `and` or `or` inside one expression or list-like condition, for example a documented condition accepting several possible values. That is different from composing two arbitrary conditions.

Therefore, do not mechanically replace every `and` or `or`. Determine whether the word belongs to the registered expression pattern or is being used as an assumed general boolean operator. Confirm ambiguous cases in the target documentation.

The rationale and merged feature are recorded in upstream discussion [#5152](https://github.com/SkriptLang/Skript/discussions/5152) and its linked implementation. Older versions predating that feature require version-appropriate alternatives.

## Never emit an empty return

The standard return effect has a value expression:

```skript
function doubled(value: number) :: number:
	return {_value} * 2
```

A bare return is not a void-control-flow statement:

```skript
# Invalid in standard Skript
function announce_if_present(message: text):
	if {_message} is not set:
		return
	broadcast {_message}
```

End a void function early with the target version's documented `stop` or `exit` control-flow effect instead. Select the narrowest form whose documented scope is the function/trigger that should end; do not guess an `exit` phrase from another language or addon.

```skript
function announce_if_present(message: text):
	if {_message} is not set:
		stop
	broadcast {_message}
```

For a value-returning function, return a value compatible with its declared return type on every path that needs a result. Do not use an empty return to represent absence. Model absence using a documented optional/default design or restructure the caller, with behavior verified for the target version.

The official return effect pattern is `return %objects%`; parser diagnostics also explicitly direct void functions toward `stop` or `exit` rather than `return`.

## Keep line-oriented structure intact

Skript normally treats each code line as a complete registered effect or section header. Avoid formatting borrowed from brace-based languages:

- Do not add `{}` around blocks.
- Do not add semicolons.
- Do not break an expression across lines merely for visual wrapping.
- Do not place a free-standing value or condition where an effect is required.
- End a section header with `:` and indent its contents consistently.

If a generated line is too long, decompose the computation into named locals or functions rather than inserting arbitrary line breaks.

## Keep function concepts distinct

- A parameter named `player` is referenced as the local variable `{_player}` in the function body; it does not automatically become the event value `player`.
- Do not use `return value` from a function that declares no return type.
- Do not use Java/Kotlin arrow return types or Python annotations. Use only the function-signature form documented for the target Skript version, such as `:: type` or a documented `returns type` form.
- Do not assume conditions are ordinary boolean expressions. Use documented conversion syntax such as `whether <condition>` only when supported by the target version.
- Do not invent named arguments, overloads, or parameter modifiers for older versions merely because a newer release documents them.

## Review pass

Before presenting code, search it for these high-signal mistakes:

```text
multiline function declaration or call
&&, ||, or programming-language !
arbitrary condition AND/OR condition on one line
bare return
return from a void function
brace-delimited blocks or semicolons
parameter referenced without {_...}
line break inserted inside an expression
```

Treat each hit as a review prompt, not a blind replacement rule. Verify the exact registered pattern and target version before changing syntax.
