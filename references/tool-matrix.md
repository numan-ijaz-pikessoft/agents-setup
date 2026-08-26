# Tool matrix — who reads what

**Use this file instead of guessing.** Writing a config file in a format a tool does not
read is what produces dead duplicate folders. If a tool is not listed here, do not invent a
path for it — say so in the report and leave it alone.

Verified August 2026. Re-check before trusting any row older than a few months.

## Native `AGENTS.md` support

These read `AGENTS.md` from the repository root with **no configuration file at all**.
Create nothing for them.

| Tool | Notes |
|---|---|
| **Codex** (OpenAI) | `AGENTS.md` is its native format. |
| **Cursor** | Reads `AGENTS.md`. `.cursor/rules/*.mdc` remains available for glob-scoped rules. |
| **Antigravity** (Google) | Reads `AGENTS.md` from v1.20.3 (Mar 2026), alongside `GEMINI.md`. Older builds need the `.agents/rules/` fallback. |
| **Windsurf / Devin** | Reads `AGENTS.md`. `.windsurf/rules/` still honoured; current builds prefer `.devin/rules/`. |
| **GitHub Copilot** | Recent versions read `AGENTS.md` in addition to `.github/copilot-instructions.md`. |

## Needs an adapter

| Tool | Path | Format |
|---|---|---|
| **Claude Code** | `CLAUDE.md` | Markdown. Supports `@AGENTS.md` imports — use the import, never a copy. |
| **Gemini CLI** | `.gemini/settings.json` | JSON. Defaults to `GEMINI.md`; set `context.fileName` to include `AGENTS.md`. Older builds used a top-level `contextFileName` key. |

## Optional belt-and-braces adapters

Create these only when the user asks, or when the repo must support older tool versions.
Each is a pointer; none carries rule text.

| Tool | Path | Frontmatter |
|---|---|---|
| Cursor | `.cursor/rules/*.mdc` | `alwaysApply: true`, or `globs: <pattern>` for scoped triggers |
| Antigravity | `.agents/rules/*.md` | none required; supports `@relative/path` references, resolved from the rules file's own location. Backward-compatible path is `.agent/rules/` |
| Windsurf | `.windsurf/rules/*.md` | `trigger: always_on` (also `manual`, `glob`, `model_decision`) |
| Copilot | `.github/copilot-instructions.md` | plain Markdown |

## Hard limits

- **Antigravity caps a rules file at 12,000 characters.** This is the binding constraint on
  `AGENTS.md` size. Anything longer belongs in a linked rule file.
- Cursor `.mdc` files use YAML frontmatter; a missing `globs` or `alwaysApply` key means the
  rule may never fire.
- Gemini CLI's `context.fileName` accepts a string or an array of strings.

## Formats deliberately not written

- `.cursorrules` — superseded by `.cursor/rules/`; do not create it in a new setup.
- `.windsurfrules` — superseded by `.windsurf/rules/`.
- `GEMINI.md` — unnecessary once `.gemini/settings.json` points at `AGENTS.md`. Creating both
  is exactly the duplication this plugin exists to prevent.
