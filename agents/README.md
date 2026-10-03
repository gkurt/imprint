# Agent instructions

Convention: **`AGENTS.md` is the single source of truth**; `CLAUDE.md` is a
one-line pointer (`@AGENTS.md`) so Claude Code picks it up too. Don't duplicate
content between them.

- [`AGENTS.base.md`](AGENTS.base.md) → copy to `AGENTS.md`, then fill in the
  project-specific `Commands`, `Project Structure`, and `Architecture` sections.
  The rest is identical across repos — keep it as-is.
- Keep `AGENTS.md` short: judgment calls every task needs. Anything a tool can check
  (formatting, import style, banned APIs, …) belongs in
  [`config/oxlintrc.json`](../config/oxlintrc.json) / [`oxfmtrc.json`](../config/oxfmtrc.json)
  / [`tsconfig.json`](../config/tsconfig.json); anything only some tasks need belongs in a skill.
- [`CLAUDE.md`](CLAUDE.md) → copy to `CLAUDE.md` verbatim.
- [`skills/changelog/`](skills/changelog/) → `.claude/skills/changelog/` in repos that
  release with Tegami. Teaches agents to write `.tegami/<slug>.md` entries.

Any other assistant that reads its own file (`.cursorrules`, `.github/copilot-instructions.md`)
should also just point at `AGENTS.md` rather than fork the rules.
