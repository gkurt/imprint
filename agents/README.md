# Agent instructions

Convention: **`AGENTS.md` is the single source of truth**; `CLAUDE.md` is a
one-line pointer (`@AGENTS.md`) so Claude Code picks it up too. Don't duplicate
content between them.

- [`AGENTS.base.md`](AGENTS.base.md) → copy to `AGENTS.md`, then fill in the
  project-specific `Commands`, `Project Structure`, and `Architecture` sections.
  The `Coding Conventions` block is identical across repos — keep it as-is.
- [`CLAUDE.md`](CLAUDE.md) → copy to `CLAUDE.md` verbatim.

Any other assistant that reads its own file (`.cursorrules`, `.github/copilot-instructions.md`)
should also just point at `AGENTS.md` rather than fork the rules.
