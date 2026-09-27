# Stack & library preferences

The defaults I reach for. Not dogma — pick the right tool per project — but this
is the starting point unless there's a reason to deviate.

**Always use the latest version of every package.** Install with a bare
`bun add <pkg>` and don't pin or cap versions. If a peer-dependency range lags
behind the latest release, still take the latest.

## Core stack

| Concern | Default | Notes |
| --- | --- | --- |
| Runtime & package manager | **Bun** | `bunfig.toml` with isolated linker. `bun i`, `bun test`, `bun run`. |
| Language | **TypeScript**, strict, ESM | `"type": "module"`, `nodenext`, `verbatimModuleSyntax`, `.ts` extension imports. |
| Type checker | **TypeScript 7** (native `tsc`) | TS7 ships the native (Go) compiler as `tsc` — no separate `tsgo` binary. `typecheck` script is `tsc`. In the editor: `TypeScriptTeam.native-preview` + `js/ts.experimental.useTsgo` (see [`ide/`](../ide/)). |
| Lint + format | **Oxlint** + **Oxfmt** | The Oxc toolchain, replaces ESLint **and** Prettier. Never add Prettier. Oxlint has the React Compiler rules built in and runs type-aware rules via `oxlint-tsgolint`; Oxfmt sorts imports. |
| Testing | **`bun test`** | Config in `bunfig.toml` (`onlyFailures = true`). Vitest only for non-Bun libs. |
| E2E | **Playwright** | Its own `e2e/` workspace + a CI job. |
| Build | match the artifact, **ESM-only** | `tsdown` or `bun build --compile` for Bun libs/CLIs; Vite/esbuild for extensions. Ship ESM only — no CJS/dual publishing. Typecheck (`noEmit`) is separate from build. |
| Release | **Tegami** | Successor to Changesets. npm trusted publishing via OIDC. |
| Git hooks | **husky** + **lint-staged** | `oxlint --fix` + `oxfmt` on staged files. |

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
`lint` (`oxlint`) / `format` (`oxfmt`) / `check` / `fix`, `test`, and the composite gate
`checks` = `bun check && bun typecheck && bun run test`. `prepare: husky`,
`tegami: bun scripts/tegami.mts`.

## Libraries by need

- **Schema / validation**: **Zod v4** — always `import * as z from 'zod/v4'` (Oxlint bans bare `zod` and `zod/v3`). Use `@standard-schema/spec` for schema-agnostic public APIs.
- **Web / sites**: **Astro** (v6) + MDX/RSS/sitemap, deployed to GitHub Pages. **React 19** (`react-jsx` runtime).
- **React Compiler**: always on, via the **Rust port** (`oxc-transform-react`) — not `babel-plugin-react-compiler`. See [React Compiler](#react-compiler) below.
- **Styling / UI**: **Tailwind CSS v4** (`@tailwindcss/vite`), **shadcn**, `clsx` + `tailwind-merge`, `class-variance-authority` / `tailwind-variants`, `tw-animate-css`. **`@base-ui/react`** for primitives. Icons via `react-icons` or `lucide-react`. Fonts via `@fontsource-variable/geist`.
- **Utilities**: **`es-toolkit`** (not lodash), `immer`.
- **AI**: Vercel **`ai`** SDK.
- **Desktop / native**: **Tauri 2** (Rust); **Zig** for native bits.
- **Testing libs**: `@testing-library/react` + `happy-dom`; Remotion for video examples.

## React Compiler

Any React project gets the [React Compiler](https://react.dev/learn/react-compiler)
through its Rust port, [`oxc-transform-react`](https://npmx.dev/package/oxc-transform-react),
which runs inside the Vite/Oxc pipeline, so there's no Babel pass. Never add
`babel-plugin-react-compiler`, `@rolldown/plugin-babel`, or `@babel/core` just for
the compiler.

- **Install** the latest of each: `bun add -d vite @vitejs/plugin-react oxc-transform-react`
  (Astro: `@astrojs/react` in place of `@vitejs/plugin-react`).
  `oxc-transform-react` is an optional peer dependency, so it has to be added explicitly.
- **Vite**: `react({ compiler: true })` in `vite.config.ts`.
- **Astro**: `react({ compiler: true })` in `astro.config.mjs` `integrations`. It
  compiles client components and hooks only, not server rendering.
- **Options**: pass [compiler options](https://react.dev/reference/react-compiler/configuration)
  in place of `true`, e.g. `compiler: { compilationMode: 'annotation' }` to
  adopt it gradually in an existing codebase. `logDiagnostics: true` prints
  recoverable bail-outs.
- **Lint**: nothing extra. The React Compiler rules are already built into Oxlint
  ([`config/oxlintrc.json`](../config/oxlintrc.json)).

The integration is still marked experimental. For an existing production app,
turn it on in a branch and run the e2e suite before merging.

## Setup instructions per archetype

- **Bun library / CLI monorepo** — the dominant style. Everything above applies. Start from `config/package.template.json`, add `packages/<pkg>` with the dev/prod-condition exports.
- **Standalone ESM library** — single package, **ESM-only** (`"type": "module"`, `exports` with an `import` condition only, no `main`/CJS). Build `dist` with `tsdown`. Don't dual-publish CJS — consumers on modern Node/Bun don't need it.
- **Vite + React app** — Vite 8 with `@vitejs/plugin-react` and `compiler: true` (see [React Compiler](#react-compiler)), Tailwind v4 via `@tailwindcss/vite`, shadcn on `@base-ui/react`. In `tsconfig.json` add `"DOM"` and `"DOM.Iterable"` to `lib` and `"vite/client"` to `types`. Scripts: `dev` = `vite`, `build` = `vite build`, `start` = `vite preview`. Component tests use `bun test` with `happy-dom` + `@testing-library/react`; Playwright covers e2e.
- **VS Code extension** — Vite lib mode or esbuild, `vscode` externalized. ESLint is tolerated here (the one place Oxlint doesn't fully fit).
- **Astro site** — `extends: astro/tsconfigs/strict`, Tailwind v4, shadcn, deploy to Pages. React islands get `react({ compiler: true })` (see [React Compiler](#react-compiler)).
