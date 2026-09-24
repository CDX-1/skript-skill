# Skript Development Skill

A portable [Agent Skill](https://agentskills.io/) for writing, reviewing, debugging, optimizing, and modernizing [Minecraft Skript](https://github.com/SkriptLang/Skript) code.

The skill helps an AI coding agent reason about Skript as a versioned, extensible language rather than guessing syntax from its English-like appearance. It emphasizes:

- version-aware core Skript syntax and addon ownership;
- common AI-generated syntax mistakes, including multiline calls, foreign boolean operators, and bare `return` statements;
- safe persistent-variable and storage design;
- event context, waits, lifecycle behavior, and server-thread constraints;
- performance for hot events, large collections, and recurring work; and
- reload-based validation against the actual server, Skript, and addon versions.

It is intended for `.sk` files and Skript architecture. It is not a Java/Kotlin plugin-development skill.

## Install

### Recommended: Skills CLI

The open-source [`skills` CLI](https://github.com/vercel-labs/skills) detects supported agents and installs the skill in the correct location:

```bash
npx skills@latest add CDX-1/skript-skill
```

The default is a project-local installation. Add `-g` to make the skill available across projects:

```bash
npx skills@latest add CDX-1/skript-skill -g
```

Target one or more specific harnesses with `-a`:

```bash
# Codex
npx skills@latest add CDX-1/skript-skill -g -a codex -y

# Claude Code
npx skills@latest add CDX-1/skript-skill -g -a claude-code -y

# Cursor
npx skills@latest add CDX-1/skript-skill -g -a cursor -y

# Gemini CLI
npx skills@latest add CDX-1/skript-skill -g -a gemini-cli -y

# GitHub Copilot
npx skills@latest add CDX-1/skript-skill -g -a github-copilot -y

# OpenCode
npx skills@latest add CDX-1/skript-skill -g -a opencode -y

# Several agents at once
npx skills@latest add CDX-1/skript-skill -g -a codex -a claude-code -a cursor -y
```

Other supported targets include Cline, Continue, Goose, Kilo Code, Kiro CLI, Roo Code, Warp, Windsurf, Zed, and many more. Run `npx skills@latest --help` for the current list and options. The CLI is preferred because agent paths and supported targets can change.

To update or remove the skill later:

```bash
npx skills@latest update skript-development -g
npx skills@latest remove skript-development -g -y
```

### Manual installation

Clone the repository so that `SKILL.md` is directly inside a skill directory named `skript-development`:

```bash
git clone https://github.com/CDX-1/skript-skill.git .agents/skills/skript-development
```

`.agents/skills` is the shared project-level convention supported by many current harnesses. For a harness-specific or global installation, clone to the applicable directory:

| Agent or harness | Project-local parent | User-level parent |
| --- | --- | --- |
| Codex | `.agents/skills/` | `~/.codex/skills/` |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Cursor | `.agents/skills/` | `~/.cursor/skills/` |
| Gemini CLI | `.agents/skills/` | `~/.gemini/skills/` |
| GitHub Copilot | `.agents/skills/` | `~/.copilot/skills/` |
| OpenCode | `.agents/skills/` | `~/.config/opencode/skills/` |
| Cline | `.agents/skills/` | `~/.agents/skills/` |
| Continue | `.continue/skills/` | `~/.continue/skills/` |
| Windsurf | `.windsurf/skills/` | `~/.codeium/windsurf/skills/` |

On Windows, `~` means your user profile directory. Restart the agent or begin a new session if it does not discover newly installed skills immediately.

### Any Agent Skills-compatible harness

This repository uses the portable Agent Skills layout:

```text
skript-development/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
```

If your harness supports Agent Skills but is not listed above, copy or clone the entire repository into its configured skills directory. The harness must preserve the relative paths because `SKILL.md` routes the agent to the files in `references/`.

If a harness has no native skill support, provide `SKILL.md` as its system/project instructions and make the `references/` directory readable to the agent. Native installation is better because it allows progressive loading of only the references needed for a task.

## Use

Most compatible agents can select the skill automatically when a request involves Minecraft Skript. You can also invoke it explicitly by name:

```text
Use $skript-development to review this .sk file for syntax, addon dependencies, and performance.
```

Useful requests include:

```text
Use $skript-development to build a Paper 1.21 reward command using Skript 2.x with no addons.

Use $skript-development to explain these reload errors and produce the smallest safe patch.

Use $skript-development to audit this player-data system for unbounded variables and hot-event work.

Use $skript-development to migrate this script while preserving its existing variable keys.
```

For the most accurate result, include your Minecraft version, server implementation, Skript version, installed addons and versions, full reload output, and relevant `.sk` files. The skill instructs the agent to make conservative assumptions when those details are unavailable.

## How it works

[`SKILL.md`](SKILL.md) is the entry point. It establishes the environment checks, evidence standards, design rules, and validation expectations. It then loads only the reference needed for the current task:

- `common-syntax-traps.md` catches high-signal AI mistakes before code is emitted.
- `language-model.md` covers registered syntax, context, variables, functions, and commands.
- `addon-ecosystem.md` tracks which project owns non-core syntax and its compatibility.
- `runtime-and-storage.md` guides bounded state and persistence choices.
- `architecture-and-performance.md` covers hot paths, lifecycle design, and threading.
- `documentation-research.md` defines a source hierarchy for exact, version-matched syntax.
- `debugging-and-validation.md` separates parse, dependency, runtime, lifecycle, and scale failures.

The skill deliberately does not claim that code parses merely because it looks correct. A real syntax check requires loading the script with the target server, Skript, and addon versions.

## Repository contents

```text
.
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── addon-ecosystem.md
    ├── architecture-and-performance.md
    ├── common-syntax-traps.md
    ├── debugging-and-validation.md
    ├── documentation-research.md
    ├── language-model.md
    └── runtime-and-storage.md
```
