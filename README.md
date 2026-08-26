# agents-setup

One source of truth for every AI coding agent in a repository.

`CLAUDE.md` is read by Claude Code and nothing else. `GEMINI.md` is read by Gemini and
nothing else. A team on mixed tools ends up copying the same rules into five files that
immediately start drifting.

This plugin consolidates them into a single **`AGENTS.md`** — read natively by Codex, Cursor,
Antigravity and Windsurf — plus pointer-only adapters for the tools that need one.

```
   CONTENT LIVES HERE (one file)         POINTERS ONLY (1-3 lines each)
   ===========================           ==============================

                                    +---- CLAUDE.md          "@AGENTS.md"
                                    |
    +----------------------+        +---- .gemini/settings.json
    |                      |<-------+        {"context":{"fileName":["AGENTS.md"]}}
    |     AGENTS.md        |        |
    |  THE SOURCE OF TRUTH |        +---- .cursor/rules/*.mdc
    |                      |        +---- .windsurf/rules/project.md
    +----------+-----------+        +---- .agents/rules/project.md
               |                    +---- .github/copilot-instructions.md
      read natively, no config:
               ^
    +----------+-----------------------------+
    |  Codex   Cursor   Antigravity  Windsurf |
    +-----------------------------------------+
```

## The guarantee

**Changing a rule means editing one file.**

| You change | Files you edit | Who sees it |
|---|---|---|
| A rule's detail | `docs/rules/<area>.md` | everyone |
| A headline rule | `AGENTS.md` | everyone |
| A Claude hook or skill | `.claude/…` | Claude only — correctly |
| A Cursor glob trigger | `.cursor/rules/*.mdc` | Cursor only — correctly |

Adapters are edited only when a *tool* changes its config format, never when a *rule*
changes. `/agents-setup:audit` enforces this.

## Install

```
/plugin marketplace add numan-ijaz-pikessoft/agents-setup
/plugin install agents-setup@agents-md
```

Or from a local clone, without installing:

```bash
claude --plugin-dir path/to/agents-setup
```

## Use

```
/agents-setup:setup      # consolidate this repo's AI config
/agents-setup:audit      # check an existing setup for duplication and drift
```

Both are also model-invoked — asking "set this repo up for Cursor and Codex" triggers the
setup skill without the slash command.

## Not a Claude user?

`SKILL.md` is the cross-tool Agent Skills format. Copy `skills/` into your own agent's skills
directory — Gemini CLI and Windsurf both read it. Or just read
[`skills/setup/SKILL.md`](skills/setup/SKILL.md) and follow it by hand; it is written to be
legible to a human.

And once a repo is set up, non-Claude developers need nothing at all: their tool reads
`AGENTS.md` directly.

## What's inside

```
.claude-plugin/
  plugin.json           manifest
  marketplace.json      makes this repo installable
skills/
  setup/SKILL.md        five-phase consolidation
  audit/SKILL.md        duplication, links, size, stale facts, coverage
references/
  tool-matrix.md        who reads what — checked, not guessed
  agents-template.md    the AGENTS.md skeleton
  adapters/             exact content of every adapter file
```

`references/tool-matrix.md` is the important one. It records which tool reads which path and
which need nothing at all, so the skill never invents a config format — inventing formats is
what leaves dead duplicate folders behind.

## Principles

- **Never invent a project rule.** The config describes the actual repo, not an ideal one.
- **Report contradictions, never resolve them silently.** The team decides.
- **Idempotent.** Running twice equals running once.
- **Never touch application source.** Config and docs only.
- **Never commit without permission.**

## Verified against

Tool support checked August 2026. See `references/tool-matrix.md` for per-tool version
caveats — notably Antigravity's 12,000-character cap on a rules file, which is the binding
constraint on `AGENTS.md` length.

## License

MIT
