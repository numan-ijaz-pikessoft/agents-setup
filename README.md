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

## Setting it up by hand

The plugin automates this, but there is nothing magic in it. The whole pattern is six steps:

1. **Create `AGENTS.md`** at the project root.
2. **Write the rules there** — stack, common commands, conventions, and the things that
   typically trip people up. Put the traps near the top; that section earns its keep fastest.
3. **Move longer rules into `docs/rules/`** and link them from `AGENTS.md`. Keep the root file
   a hub: a summary per area plus a link, not the full text.
4. **Replace the contents of `CLAUDE.md`** with `@AGENTS.md`, leaving only genuinely
   Claude-specific things (hooks, skills) beneath it.
5. **For Gemini CLI**, add `.gemini/settings.json`:
   ```json
   { "context": { "fileName": ["AGENTS.md", "GEMINI.md"] } }
   ```
6. **Cursor, Codex, Antigravity and Windsurf** generally need no configuration — they read
   `AGENTS.md` on their own.

Optional belt-and-braces adapters for older tool versions live in
[`references/adapters/`](references/adapters/). Each is a pointer; none carries rule text.

## Worth knowing

- **Keep `AGENTS.md` under 12,000 characters.** Antigravity truncates a longer rules file, and
  it does so silently — the tail simply never arrives. This is the binding constraint on the
  file's length, and the reason detail belongs in `docs/rules/`.
- **Never let a pointer file accumulate rules of its own.** The moment an adapter holds a
  rule, that rule lives in two places and a change means two edits. This is the single
  failure mode that undoes the whole pattern, and what `/agents-setup:audit` checks first.
- **Support varies by tool version.** Antigravity added `AGENTS.md` in v1.20.3 (March 2026);
  Copilot's support is the newest of the group. If a tool appears to ignore the file, add that
  tool's own rules file pointing back to it rather than duplicating the content.
- **Concrete numbers in docs rot.** Counts like "23 services" drift within weeks. Recount when
  you touch them, or leave them out.
- **Don't create `.cursorrules` or `.windsurfrules`.** Both are superseded by `.cursor/rules/`
  and `.windsurf/rules/`.
- **If you mirror or sync the repo anywhere**, check the path filters before moving rule files.
  Rules that were excluded because they sat under `.claude/` will start being exported once
  they live in `docs/rules/`.

## Checking that it works

Open the project in each tool and ask it something only your rules would answer — for example
*"what happens if I add a controller without a role decorator?"*

A specific answer means the file was loaded. A generic one means it wasn't, and that tool
needs its adapter. About thirty seconds per tool, worth doing once for each tool your team
actually uses.

## Not a Claude user?

Follow **Setting it up by hand** above — that is the entire pattern, and it is tool-agnostic.

`SKILL.md` is the cross-tool Agent Skills format, so copying `skills/` into another agent's
skills directory may work depending on the tool and version; I have only tested the plugin
under Claude Code. Either way,
[`skills/setup/SKILL.md`](skills/setup/SKILL.md) is written to be legible to a human — reading
it and following along works fine.

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
