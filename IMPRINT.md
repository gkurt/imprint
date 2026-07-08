# IMPRINT.md

> **AI agents: this is the manifest.** It describes how Gokhan (`gkurt`) sets up a
> repository and where each preference lives. To "imprint" a repo means: apply
> these preferences to it, adapting to the project's language and archetype.

If you were invoked via the `imprint` skill, you already resolved this file from
an imprint source — a local directory or a `<username>/imprint` repo (public or
private). Read the paths below relative to that source, not the repo you're
imprinting. Follow the procedure below.

## How to imprint a repository

Work top to bottom. **Adapt, don't clobber**: detect what the target repo already
is (language, existing configs, monorepo or not), copy the relevant templates,
and merge rather than blindly overwrite. If a file already exists and differs
meaningfully, show the diff and ask before replacing.

1. **Read [`stack/README.md`](stack/README.md) first.** It's the philosophy — the
   runtime, language, and library defaults everything else assumes (Bun +
   TypeScript + Biome + ESM). Decide the project archetype before copying files.

2. **Config files** — [`config/`](config/). Copy into the repo root:
   | Source | Destination |
   | --- | --- |
   | [`config/tsconfig.json`](config/tsconfig.json) | `tsconfig.json` |
   | [`config/biome.jsonc`](config/biome.jsonc) | `biome.jsonc` |
   | [`config/bunfig.toml`](config/bunfig.toml) | `bunfig.toml` |
   | [`config/editorconfig`](config/editorconfig) | `.editorconfig` |
   | [`config/gitignore`](config/gitignore) | `.gitignore` |
   | [`config/package.template.json`](config/package.template.json) | `package.json` (fill placeholders; merge into an existing one) |
   | [`config/husky-pre-commit`](config/husky-pre-commit) | `.husky/pre-commit` (then `bunx husky init` / set `chmod +x`) |

   The dotless names (`editorconfig`, `gitignore`, `husky-pre-commit`) are stored
   that way so they don't take effect inside this repo — **rename on copy**.

3. **IDE settings** — [`ide/`](ide/). Copy `extensions.json` and `settings.json`
   into `.vscode/`. See [`ide/README.md`](ide/README.md) for per-language add-ons.

4. **Agent instructions** — [`agents/`](agents/). Copy
   [`AGENTS.base.md`](agents/AGENTS.base.md) → `AGENTS.md` and fill in the
   project-specific sections; copy [`CLAUDE.md`](agents/CLAUDE.md) verbatim.

5. **CI & release** — [`ci/`](ci/). Copy the workflows into `.github/workflows/`.
   See [`ci/README.md`](ci/README.md). Use `ci.yml` always; add the Tegami
   `release.yml` + PR-preview pair for publishable packages.

6. **GitHub repo settings** — [`github/settings.md`](github/settings.md). Apply
   branch protection, merge settings, and publishing config (ideally with `gh`).

7. **Finish**: install deps (`bun i`), run `bun run checks`, and fix anything the
   linter/formatter flags. Report what you changed and what still needs the
   user's hand (npm trusted-publishing setup, branch protection that needs the UI).

## Directory map

| Folder | Contains |
| --- | --- |
| [`stack/`](stack/) | Runtime/language/library preferences + per-archetype setup notes. **Read first.** |
| [`config/`](config/) | Drop-in config files: tsconfig, Biome, bunfig, editorconfig, gitignore, package.json template, husky hook. |
| [`ide/`](ide/) | `.vscode` extensions + settings. |
| [`agents/`](agents/) | `AGENTS.md` base + `CLAUDE.md` pointer. |
| [`ci/`](ci/) | GitHub Actions: CI + Tegami release/PR-preview workflows. |
| [`github/`](github/) | GitHub repo settings checklist. |
| [`skills/imprint/`](skills/imprint/) | The installable `imprint` skill that drives this. |

## Principles behind the choices

- **One tool per job**: Biome (not ESLint + Prettier), Bun (not Node + npm + a bundler for scripts).
- **Run source in dev, ship built code**: the `<pkg>@dev` export condition.
- **Bleeding-edge but strict**: `tsgo`, TS betas, `strict` + `noUncheckedIndexedAccess`.
- **`AGENTS.md` is canonical**; every other assistant file points at it.
- **Consistency across repos**: same script names, same formatter values, same CI shape.
