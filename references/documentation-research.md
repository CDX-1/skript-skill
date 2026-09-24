# Documentation Research

Use this reference when syntax, ownership, availability, semantics, or compatibility is uncertain. Documentation lookup is part of implementation, not an afterthought.

## Start from the target environment

Search for the user's installed versions, not merely the newest syntax. Capture the exact Minecraft, server, Java, Skript, and addon versions when compatibility matters. Use archived documentation or tagged source for older installations.

## Source hierarchy

Prefer evidence in this order, adjusting when a project designates another canonical source:

1. Version-matched official Skript documentation and release notes
2. The owning addon's version-matched official documentation and release notes
3. Tagged source, syntax registration code, annotations, tests, and bundled examples
4. The project's GitHub README or wiki
5. skUnity and SkriptHub documentation indexes
6. Maintainer answers and well-supported community examples
7. Unverified forum, paste, video, or chat examples

Official current core documentation is available at <https://docs.skriptlang.org/>. The upstream projects are under the SkriptLang organization on GitHub. Use skUnity and SkriptHub to discover syntax across addons, then confirm important entries with the owning project because community indexes can be stale, mislabeled, or indexed from another release.

## Search effectively

Search using combinations of:

- Exact syntax name or distinctive pattern words
- Category: event, effect, condition, expression, section, structure, type, or event value
- Addon name
- Target version numbers
- Return/input type
- Minecraft or Paper API concept

When documentation is vague, inspect source for registration calls, syntax annotations, version metadata, changelog entries, tests, and examples. Reading registration code establishes what pattern exists; it does not by itself prove runtime semantics, thread safety, or compatibility, so inspect the implementation and tests when those matter.

## Evaluate a result

Before using syntax, answer:

```text
What project owns it?
Which version documents or registers it?
Does that version match the user's environment?
What context and types does it require?
Is it current, deprecated, or experimental?
Is the example complete, or does it rely on another addon?
```

If multiple sources disagree, prefer the source tied most closely to the installed version. Mention the conflict when it affects the answer.

## Work with documentation indexes

On skUnity or SkriptHub:

- Filter by addon and version when possible.
- Open the full syntax entry rather than relying on search-result text.
- Check examples for undeclared addon dependencies.
- Follow the project link to releases, wiki, or source.
- Confirm the entry is not for an abandoned fork or a similarly named addon.

Treat copied snippets as leads, not authority. Syntax may have changed while old examples continue to circulate.

## Record uncertainty honestly

For substantial or risky work, maintain a compact evidence note:

```text
Construct: persistent data expression
Owner: <core or addon>
Verified version: <version>
Target version: <version>
Source: <documentation, release, or source link>
Confidence/issue: <confirmed, inferred, conflicting, or unverified>
```

Do not fabricate a minimum version or compatibility range. If a current-documentation tool cannot find a relevant entry, use the owning project's source or clearly mark the construct unverified.
