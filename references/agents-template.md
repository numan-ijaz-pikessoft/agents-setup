# AGENTS.md template

Fill only the sections the repository actually supports. **Omit a section rather than guess
at it.** An invented rule is worse than a missing one, because a future agent will follow it.

Target: **under 12,000 characters** (Antigravity's cap). If a section grows past a few
paragraphs, move the detail into a linked rule file and leave a summary plus the link.

---

```md
# AGENTS.md

> One-sentence description of what this project is, who consumes it, and its stack.

Canonical instructions for every AI coding agent working in this repository — Claude Code,
Cursor, Codex, Gemini CLI, Antigravity, Windsurf and Copilot alike. Tool-specific config
files point here; they never restate these rules.

## Quick start

Install, configure, migrate, seed, run. Real commands, copy-pasteable, with the port and any
non-obvious entry point.

## Project structure

The module or package map, one line each. Mark anything generated or gitignored.

## Development commands

build / test / lint / typecheck / format, and which one is the gate that must pass.

## The things that bite people here

The two-to-five traps that cost a newcomer a day. This is the highest-value section in the
file — put it high and keep it short. Each entry: the trap, why it exists, what to do
instead.

## Architecture

Layering rules and boundaries. What belongs in which layer, and the known debt.

## Coding standards

Naming, types, comments, file size, formatting ownership.

## API rules

Compatibility guarantees, response shapes, documentation requirements.

## Database rules

Migrations, query hygiene, transactions, ownership scoping, soft deletes.

## Testing rules

Which suite covers what, which one is the gate, fixture conventions, what must be asserted.

## Security rules

Auth, roles, ownership, input validation, secrets, PII, error disclosure.

## Git rules

Branching, commit format, commit identity, what is protected, what requires permission.

## Important constraints

The never-do list, consolidated. Short imperative lines.

## Definition of done

What must be run and reported before work is claimed complete.

## Detailed rules

Links to the per-area rule files. One line each.
```

---

## Rules for filling it in

- **Move, do not invent.** Every rule must come from an existing config file, a documented
  convention, or a pattern the code demonstrably follows.
- **Verify any number before writing it.** Counts of files, tests or offenders rot fast.
  Count them; do not copy a stale figure forward.
- **Report contradictions, never resolve them silently.** If one file says npm and another
  says pnpm, surface both and ask.
- **Keep tool-specific content out.** A hook script, an MCP server, a slash command belong in
  that tool's own directory.
