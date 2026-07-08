# Stack & library preferences

The defaults I reach for. Not dogma — pick the right tool per project — but this
is the starting point unless there's a reason to deviate.

## Core stack

| Concern | Default | Notes |
| --- | --- | --- |
| Runtime & package manager | **Bun** | `bunfig.toml` with isolated linker. `bun i`, `bun test`, `bun run`. |
| Language | **TypeScript**, strict, ESM | `"type": "module"`, `nodenext`, `verbatimModuleSyntax`, `.ts` extension imports. |
| Type checker | **TypeScript 7** (native `tsc`) | TS7 ships the native (Go) compiler as `tsc` — no separate `tsgo` binary. `typecheck` script is `tsc`. |
| Lint + format | **Biome v2** | One tool, replaces ESLint **and** Prettier. Never add Prettier. |
| Testing | **`bun test`** | Config in `bunfig.toml` (`onlyFailures = true`). Vitest only for non-Bun libs. |
| E2E | **Playwright** | Its own `e2e/` workspace + a CI job. |
| Build | match the artifact, **ESM-only** | `tsdown` or `bun build --compile` for Bun libs/CLIs; Vite/esbuild for extensions. Ship ESM only — no CJS/dual publishing. Typecheck (`noEmit`) is separate from build. |
| Release | **Tegami** | Successor to Changesets. npm trusted publishing via OIDC. |
| Git hooks | **husky** + **lint-staged** | `biome check --write` on staged files. |

## Monorepo layout

Bun workspaces: `packages/*`, `examples/*`, plus `docs`/`website` when there are docs.
Root `package.json` is `"private": true`, named `@<project>/root`. Scoped packages
`@<project>/<pkg>`.

**Dev/prod dual resolution** — the signature trick. Publishable packages export a
custom `<pkg>@dev` condition that points at raw `.ts` source, so dev and tests run
source directly while consumers get built `dist`:

```jsonc
// package.json exports
"exports": {
  ".": {
    "<pkg>@dev": "./src/index.ts",
    "import": { "types": "./dist/index.d.mts", "default": "./dist/index.mjs" },
    "source": "./src/index.ts"
  }
}
```

Paired with `tsconfig.json` `"customConditions": ["<pkg>@dev"]` and
`bun test --conditions=<pkg>@dev`. This flag is critical — without it tests resolve
to stale `dist`.

## Script vocabulary

Keep script names consistent across repos: `start`, `dev`, `typecheck` (`tsc`),
`lint` / `format` / `check` / `fix` (Biome), `test`, and the composite gate
`checks` = `bun check && bun typecheck && bun run test`. `prepare: husky`,
`tegami: bun scripts/tegami.mts`.

## Libraries by need

- **Schema / validation**: **Zod v4** — always `import * as z from 'zod/v4'` (Biome-enforced). Use `@standard-schema/spec` for schema-agnostic public APIs.
- **Web / sites**: **Astro** (v6) + MDX/RSS/sitemap, deployed to GitHub Pages. **React 19** (`react-jsx` runtime).
- **Styling / UI**: **Tailwind CSS v4** (`@tailwindcss/vite`), **shadcn**, `clsx` + `tailwind-merge`, `class-variance-authority` / `tailwind-variants`, `tw-animate-css`. **`@base-ui/react`** for primitives. Icons via `react-icons` or `lucide-react`. Fonts via `@fontsource-variable/geist`.
- **Utilities**: **`es-toolkit`** (not lodash), `immer`.
- **AI**: Vercel **`ai`** SDK.
- **Desktop / native**: **Tauri 2** (Rust); **Zig** for native bits.
- **Testing libs**: `@testing-library/react` + `happy-dom`; Remotion for video examples.

## Setup instructions per archetype

- **Bun library / CLI monorepo** — the dominant style. Everything above applies. Start from `config/package.template.json`, add `packages/<pkg>` with the dev/prod-condition exports.
- **Standalone ESM library** — single package, **ESM-only** (`"type": "module"`, `exports` with an `import` condition only, no `main`/CJS). Build `dist` with `tsdown`. Don't dual-publish CJS — consumers on modern Node/Bun don't need it.
- **VS Code extension** — Vite lib mode or esbuild, `vscode` externalized. ESLint is tolerated here (the one place Biome doesn't fully fit).
- **Astro site** — `extends: astro/tsconfigs/strict`, Tailwind v4, shadcn, deploy to Pages.
