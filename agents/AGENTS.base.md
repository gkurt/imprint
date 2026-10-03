# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

<!--
  This is a starting skeleton. Fill in the project-specific parts (Commands,
  Project Structure, Architecture) for each repo. Keep the rest — it is the
  same everywhere.
-->

## Commands

```bash
bun dev            # Run in watch mode
bun run test       # Run all tests
bun typecheck      # Type check (TypeScript 7, native tsc)
bun run lint       # Lint
bun run format     # Format
bun run fix        # Lint + format + autofix
bun run checks     # Everything: check + typecheck + test
```

Prefer these scripts over ad-hoc commands. Do not prefix them with `bun run` when
a bare alias exists (`bun check`, `bun typecheck`) — those are whitelisted for
agent use. Before finishing, run `bun run fix` then `bun run checks` — the linter,
formatter and `tsc` own all mechanical style rules. Fix the code; don't disable rules.

## Project Structure

<!-- Annotated, file-by-file map of the important modules. Keep it current. -->

## Architecture

<!-- How the pieces fit together: data flow, key abstractions, and the non-obvious decisions behind them. -->

## Coding Conventions

- Prefer colocation.
- Avoid verbose code comments; write self-explanatory code. Comments are acceptable for:
  - Explaining complex logic, workarounds, or decisions
  - Documenting public APIs (functions, classes, modules)
  - TODO/FIXME notes
  - When the user specifically asks for comments
- Prefer early returns and guard clauses over nested conditionals.
- Check for existing utilities/hooks/components before creating new ones. Avoid duplication.
- Remove dead and commented-out code; don't preserve old APIs unless asked.
- When moving or relocating code (functions, components, utilities), don't leave a re-export behind for backwards compatibility. Update every importer to point at the new location and delete the old definition, so there is a single source of truth.

## Documentation

When changing user-facing APIs, update all relevant docs in the same change:
docs pages, README.md, SKILL.md, AGENTS.md, llms.txt. Documentation must not go stale.
