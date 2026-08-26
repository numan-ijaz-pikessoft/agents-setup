---
name: setup
description: Consolidate a repository's AI-agent instructions into one AGENTS.md read by Claude Code, Cursor, Codex, Gemini CLI, Antigravity, Windsurf and Copilot, with pointer-only adapters instead of duplicated rule files. Use when asked to set up AGENTS.md, configure a repo for multiple AI coding agents, stop duplicating CLAUDE.md across tools, or make project rules work in Cursor/Codex/Gemini.
---

# Set up one source of truth for every AI agent

`CLAUDE.md` is read by Claude Code and nothing else. `GEMINI.md` is read by Gemini and
nothing else. A team on mixed tools ends up copying the same rules into five files that
immediately start drifting.

This skill produces one file with content — **`AGENTS.md`** — and pointer-only adapters for
the tools that need one.

**The guarantee to preserve: changing a rule must mean editing exactly one file.** An adapter
that contains rule text breaks that guarantee. Adapters point; they never restate.

## Before you start

Read [`references/tool-matrix.md`](../../references/tool-matrix.md). It records which tool
reads which path, and which need nothing at all. **Never invent a config path for a tool that
is not listed there** — a file in a format nothing reads is the exact failure this skill
exists to prevent.

## Phase 1 — Discover

Do not write anything yet.

1. Find the repository root and confirm it is a repo.
2. Identify the stack, package manager, and the real build / test / lint / typecheck
   commands — read `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`, and
   the CI workflow. **CI is the most reliable source for which command is the gate.**
3. Find every existing AI config file:
   ```
   AGENTS.md  CLAUDE.md  GEMINI.md  .cursorrules  .windsurfrules
   .claude/  .cursor/  .windsurf/  .gemini/  .agents/  .agent/  .github/copilot-instructions.md
   ```
4. Read all of them, plus `README.md` and `CONTRIBUTING.md`.
5. Note which existing rules are **shared** (apply to any agent), **tool-specific** (hooks,
   MCP servers, slash commands), or **developer-specific** (personal preference — these do
   not belong in repo config at all).

Report what you found before changing anything.

## Phase 2 — Preserve and consolidate

- **Never overwrite blindly.** Existing instructions are the product of real experience.
- If a good `AGENTS.md` already exists, improve it rather than replace it.
- **Contradictions are reported, not resolved.** If one file says npm and another pnpm, or
  one says tabs and another spaces, stop and ask. Choosing silently hides a decision the team
  needs to make.
- Shared rules move to `AGENTS.md`. Tool-specific config stays in its tool's directory.
  Developer-specific preferences are dropped, and you say so.

### Rules living in a tool-specific directory

General engineering rules under `.claude/rules/`, `.cursor/rules/` and the like are shared
rules wearing a tool's badge. Propose moving them somewhere neutral — `docs/rules/` is a good
default — and **rewrite every inbound link in the same change**. Find them first:

```bash
grep -rn "<old-rules-path>" . --exclude-dir=node_modules --exclude-dir=.git
```

Skills, hook scripts and other rule files all cross-reference each other. A move that leaves
a dangling link is worse than no move.

## Phase 3 — Write AGENTS.md

Use [`references/agents-template.md`](../../references/agents-template.md).

- Fill only sections the repo actually supports. **Omit rather than guess.**
- **Do not invent rules.** No "always use TypeScript", "always use REST", "always use Clean
  Architecture" unless the repo already demonstrates or documents it. The file must describe
  the actual project, not an idealised one.
- **Verify every number.** Counts of controllers, services, tests or offenders rot. Count
  them yourself; never copy a stale figure forward. Report any drift you corrected.
- **Stay under 12,000 characters** — Antigravity's cap. Overflow goes into linked rule files
  with a summary and a link left behind.
- Put the footgun section high. It is the part that saves the most time.

## Phase 4 — Generate adapters

Copy from [`references/adapters/`](../../references/adapters/), substituting the `{{...}}`
placeholders. Create only what the tool matrix says is needed:

| Tool | File | Always needed? |
|---|---|---|
| Claude Code | `CLAUDE.md` | yes — uses `@AGENTS.md` import |
| Gemini CLI | `.gemini/settings.json` | yes — it defaults to `GEMINI.md` |
| Cursor, Codex, Antigravity, Windsurf, Copilot | — | no, they read `AGENTS.md` natively |

Optional belt-and-braces adapters (`.cursor/rules/`, `.windsurf/rules/`, `.agents/rules/`,
`.github/copilot-instructions.md`) are for older tool versions or an explicit request. Ask
before creating a file nothing currently requires.

**Every adapter is a pointer.** Two sentences naming `AGENTS.md` as authoritative. No rule
text, no footgun summaries, no "quick reminders" — those all have to be re-edited when the
rule changes, which is the duplication this skill removes.

The one exception is a **glob-scoped** Cursor rule, whose value is *when* it fires rather than
what it says. It still carries no rule text — it links to the rule file.

## Phase 5 — Report

- **Created** / **Updated** / **Preserved** — file lists.
- **Consolidated** — which rules moved where, and every link rewritten.
- **Conflicts** — contradictions found, unresolved, with both sources quoted.
- **Corrections** — stale facts or counts you fixed, old value and new.
- **Not created** — tools you deliberately wrote nothing for, and why.
- **Final structure** — a tree.

## Constraints

- **Do not modify application source.** Config and docs only.
- **Do not commit.** Leave changes in the working tree and report them, unless the user
  explicitly asks. Check the repo's own git rules first — many forbid unprompted commits.
- **Idempotent.** Running twice produces the same result as running once: no duplicated
  sections, no appended repeats, manual edits preserved.
- **No secrets** in any file you write.
