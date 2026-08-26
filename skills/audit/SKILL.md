---
name: audit
description: Audit a repository's AI-agent configuration for duplicated rules, adapters that have grown rule text, broken links, oversized AGENTS.md, missing adapters, and stale facts that no longer match the code. Use to check an AGENTS.md setup is still healthy, before onboarding a new agent tool, or when project rules seem to have drifted from reality.
---

# Audit the AI-agent configuration

A consolidated setup decays in predictable ways. This skill finds the decay. It is
**read-only by default** — report findings, then fix only what the user approves.

Read [`references/tool-matrix.md`](../../references/tool-matrix.md) first for the current
paths and limits every check below depends on.

## 1. Adapter purity — the most important check

The whole design rests on one guarantee: **changing a rule means editing one file.** An
adapter that has grown rule text silently breaks it — that rule now lives in two places and
will drift.

An adapter is healthy when it names `AGENTS.md` as authoritative and stops.

```bash
wc -l CLAUDE.md .cursor/rules/*.mdc .windsurf/rules/*.md \
      .agents/rules/*.md .github/copilot-instructions.md 2>/dev/null
```

Flag any adapter that:
- runs beyond roughly a dozen lines,
- restates a sentence found in `AGENTS.md` or the rule files,
- lists commands, footguns, or "quick reminders",
- names a rule instead of linking to it.

Report each as a **duplication bug**, with the number of files that would need editing if
that rule changed. A glob-scoped Cursor rule is the sole exception, and only because its
value is *when* it fires — it must still link rather than restate.

## 2. Broken links

Every relative link in the config must resolve. Extract and check them:

```bash
grep -rhoE '\]\([^)]+\.md[^)]*\)' AGENTS.md CLAUDE.md docs/rules/ .claude/ 2>/dev/null
```

Resolve each path relative to its containing file. Check especially for links pointing at a
directory that was moved — a rules move that missed an inbound link is the most common
breakage.

## 3. Size budget

Antigravity caps a rules file at **12,000 characters**:

```bash
wc -c AGENTS.md .agents/rules/*.md 2>/dev/null
```

Over budget means the tail is silently truncated for some agents. Fix by moving detail into a
linked rule file, never by deleting a rule.

## 4. Stale facts

The failure that erodes trust fastest: `AGENTS.md` asserts something the code no longer
supports. Every concrete claim is checkable — verify each one:

- Counts of files, services, controllers, tests, offenders — recount them.
- Named files, functions, scripts, env vars — confirm they still exist.
- Commands — confirm they exist in `package.json` or the equivalent.
- "The suite is green" — that is a claim, and it needs a run to stand.

Report each drift as *claimed → actual*.

## 5. Coverage

Cross-check the tool matrix against what is present:

- A tool the team uses with no adapter and no native `AGENTS.md` support → **gap**.
- An adapter for a tool nobody uses → **dead file**, propose removing it.
- A superseded format (`.cursorrules`, `.windsurfrules`, a `GEMINI.md` alongside a
  `.gemini/settings.json` that already points at `AGENTS.md`) → **duplication**, propose
  removing it.

## 6. Contradictions

Compare every instruction source that survived. Package manager, formatting, branch names,
commit conventions. Report both sides with file and line. **Never resolve a contradiction
silently** — the team has to decide.

## Reporting

Lead with the highest-severity, most certain finding. Order: duplication bugs, broken links,
stale facts, size, coverage, contradictions. For each: the file and line, what breaks
because of it, and the fix.

If everything passes, say so plainly and state what you checked — a clean audit is only
useful if its scope is visible.
