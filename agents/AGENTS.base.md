# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

<!--
  This is a starting skeleton. Fill in the project-specific parts (Commands,
  Project Structure, Architecture) for each repo. Keep the Coding Conventions
  section — it is the same everywhere.
-->

## Commands

```bash
bun run test       # Run all tests
bun typecheck      # Type check (TypeScript 7, native tsc)
bun run lint       # Lint
bun run format     # Format
bun run fix        # Lint + format + autofix
bun run checks     # Everything: check + typecheck + test
```

Prefer these scripts over ad-hoc commands. Do not prefix them with `bun run` when
a bare alias exists (`bun check`, `bun typecheck`) — those are whitelisted for
agent use.

## Project Structure

<!-- Annotated, file-by-file map of the important modules. Keep it current. -->

## Key Conventions

- **Runtime**: Bun. **Language**: TypeScript (strict, ESNext, `nodenext` modules).
- **Formatting**: Biome — 2-space indent, single quotes, 140 char line width, LF.
- **Imports**: use `.ts` extensions in source imports (`verbatimModuleSyntax` is on).
  Internal modules use the `#*` subpath alias (e.g. `import { env } from '#src/env.ts'`).
- **Zod**: always `import * as z from 'zod/v4'` — never bare `zod` or `zod/v3`. Enforced by Biome.

## Coding Conventions

- Prefer colocation.
- Use TypeScript with strict typing. Avoid `any` unless absolutely necessary.
- When importing internal modules, use absolute imports starting with `#src/`. Also include file extensions.
- Always use top-level `import type` for type imports. Never use inline
  `import('./module.ts').Type` syntax in type annotations.
- Avoid verbose code comments; write self-explanatory code. Comments are acceptable for:
  - Explaining complex logic, workarounds, or decisions
  - Documenting public APIs (functions, classes, modules)
  - TODO/FIXME notes
  - When the user specifically asks for comments
- Prefer concise, clear code:
  - Prefer early returns to reduce nesting.
  - Prefer single-line `if` statements for simple conditions.
- If a file gets too long (e.g. >600 lines), refactor into smaller modules.
- Check for existing utilities/hooks/components before creating new ones. Avoid duplication.
- Remove dead and commented-out code; don't preserve old APIs unless asked.
- When moving or relocating code (functions, components, utilities), don't leave a re-export behind for backwards compatibility. Update every importer to point at the new location and delete the old definition, so there is a single source of truth.
<!-- For React Projects -->
- Variables and regular functions shouldn't be prefixed with `use`. The `use` prefix should be reserved for React hooks.
- Don't use `forwardRef`. React 19 supports passing refs to function components directly, so `forwardRef` is no longer necessary and adds unnecessary complexity.
- Avoid `useEffect` unless absolutely necessary; prefer custom hooks.
  - If you decided to use `useEffect`, read this first, and reevaluate your decision: https://react.dev/learn/you-might-not-need-an-effect
  - Always put a comment explaining what the effect does.

## Documentation

When changing user-facing APIs, update all relevant docs in the same change:
docs pages, README.md, SKILL.md, AGENTS.md, llms.txt. Documentation must not go stale.

## Changelogs

Releases are managed by [Tegami](https://tegami.fuma-nama.dev) (config in
`scripts/tegami.mts`). When asked to commit with a changelog entry, run
`bun run tegami` or add a `.tegami/*.md` file directly. Each entry has
`packages:` frontmatter (package + bump type) and a body with at least one
heading. Keep entries concise — user-facing changes only, no implementation detail.
