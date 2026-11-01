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
bun check          # Type check (Bun's built-in checker, same errors as TypeScript 7 tsc)
bun fmt            # Format (Bun's built-in formatter)
bun run lint       # Lint + format check
bun run fix        # Lint autofix + format
bun run checks     # Everything: lint + type check + test
```

Prefer these commands over ad-hoc ones. `bun check` and `bun fmt` are Bun built-ins,
not scripts — never add a `check` or `fmt` script, it would shadow them. Before
finishing, run `bun run fix` then `bun run checks` — the linter, formatter and type
checker own all mechanical style rules. Fix the code; don't disable rules.

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
- Line endings are LF, on every OS. Scripts that write files must write LF too: on Windows, Python's text mode writes CRLF unless opened with `newline=''`.
- Don't edit `version` in `package.json` by hand; the release workflow bumps it from the changelog entries. Change it yourself only when the user asks.
- When moving or relocating code (functions, components, utilities), don't leave a re-export behind for backwards compatibility. Update every importer to point at the new location and delete the old definition, so there is a single source of truth.

## Commits

Commit after each milestone: once a coherent step works and `bun run checks`
passes, commit it before starting the next one. Don't save everything for a single
commit at the end.

## Documentation

When changing user-facing APIs, update all relevant docs in the same change:
docs pages, README.md, SKILL.md, AGENTS.md, llms.txt. Documentation must not go stale.
