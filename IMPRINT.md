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
   TypeScript + Oxlint/Oxfmt + ESM). Decide the project archetype before copying files.

2. **Config files** — [`config/`](config/). Copy into the repo root:
   | Source | Destination |
   | --- | --- |
   | [`config/tsconfig.json`](config/tsconfig.json) | `tsconfig.json` |
   | [`config/oxlintrc.json`](config/oxlintrc.json) | `.oxlintrc.json` |
   | [`config/oxlint-plugin.js`](config/oxlint-plugin.js) | `scripts/oxlint-plugin.js` (local rules `.oxlintrc.json` loads) |
   | [`config/oxfmtrc.json`](config/oxfmtrc.json) | `.oxfmtrc.json` |
   | [`config/bunfig.toml`](config/bunfig.toml) | `bunfig.toml` |
   | [`config/editorconfig`](config/editorconfig) | `.editorconfig` |
   | [`config/gitignore`](config/gitignore) | `.gitignore` |
   | [`config/package.template.json`](config/package.template.json) | `package.json` (fill placeholders; merge into an existing one) |
   | [`config/tsconfig.project.json`](config/tsconfig.project.json) | `packages/<pkg>/tsconfig.json` in monorepos |
   | [`config/husky-pre-commit`](config/husky-pre-commit) | `.husky/pre-commit` (then `bunx husky init` / set `chmod +x`) |

   Then install the tooling at its latest version (the template carries no versions):

   ```bash
   bun add -d @types/bun typescript oxlint oxlint-tsgolint oxfmt husky lint-staged
   ```

   The dotless names (`oxlintrc.json`, `oxfmtrc.json`, `editorconfig`, `gitignore`, `husky-pre-commit`) are stored
   that way so they don't take effect inside this repo — **rename on copy**.

3. **IDE settings** — [`ide/`](ide/). Copy `extensions.json` and `settings.json`
   into `.vscode/`. See [`ide/README.md`](ide/README.md) for per-language add-ons.

4. **Agent instructions** — [`agents/`](agents/). Copy
   [`AGENTS.base.md`](agents/AGENTS.base.md) → `AGENTS.md` and fill in the
   project-specific sections; copy [`CLAUDE.md`](agents/CLAUDE.md) verbatim.

5. **CI & release** — [`ci/`](ci/). Copy the workflows into `.github/workflows/`.
   See [`ci/README.md`](ci/README.md). Use `ci.yml` always; add the Tegami
   `release.yml` + PR-preview pair for publishable packages, plus
   [`ci/tegami.mts`](ci/tegami.mts) → `scripts/tegami.mts` (fill in the repo slug
   and the primary package name).

6. **Agent skills** — [`skills/`](skills/). Install every skill there except `imprint`
   that fits the project into `.claude/skills/<name>/`. Each skill's entry in
   [`skills/README.md`](skills/README.md) and its `SKILL.md` description say when it
   applies (e.g. `changelog` only with the Tegami release setup from step 5). If the
   repo already has the skill, update it rather than adding a second copy.

7. **GitHub repo settings** — [`github/settings.md`](github/settings.md). Apply
   branch protection, merge settings, and publishing config (ideally with `gh`).

8. **Finish**: install deps (`bun i`), run `bun run checks`, and fix anything the
   linter/formatter flags. Report what you changed, which skills you installed or
   skipped, and what still needs the user's hand (npm trusted-publishing setup,
   branch protection that needs the UI).

## Directory map

| Folder | Contains |
| --- | --- |
| [`stack/`](stack/) | Runtime/language/library preferences + per-archetype setup notes. **Read first.** |
| [`config/`](config/) | Drop-in config files: tsconfig, Oxlint (+ local rules plugin), Oxfmt, bunfig, editorconfig, gitignore, package.json template, husky hook. |
| [`ide/`](ide/) | `.vscode` extensions + settings. |
| [`agents/`](agents/) | `AGENTS.md` base and `CLAUDE.md` pointer. |
| [`ci/`](ci/) | GitHub Actions: CI + Tegami release/PR-preview workflows, and the `scripts/tegami.mts` config. |
| [`github/`](github/) | GitHub repo settings checklist. |
| [`skills/`](skills/) | Agent skills: [`imprint`](skills/imprint/), which drives this, and the skills step 6 installs into projects. |

## Principles behind the choices

- **One toolchain per job**: Oxc — Oxlint + Oxfmt (not ESLint + Prettier), Bun (not Node + npm + a bundler for scripts).
- **Run source in dev, ship built code**: the `<pkg>@dev` export condition.
- **Modern but strict**: TypeScript 7 (native `tsc`), ESM-only, `strict` + `noUncheckedIndexedAccess`.
- **`AGENTS.md` is canonical**; every other assistant file points at it. It holds only what every task needs — mechanical rules go in lint/format/tsconfig, task-specific ones in skills.
- **Consistency across repos**: same script names, same formatter values, same CI shape.
