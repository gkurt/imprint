# Forking — create your own imprint

This is the `fork` mode of the imprint skill. Instead of *applying* an imprint to
a repo, it produces the caller's **own** `<their-handle>/imprint`: a copy of the
source imprint, repopulated with *their* preferences and scrubbed of the original
author's identity.

Reach this flow only when the skill is invoked with the `fork` argument. The
normal apply flow lives in `SKILL.md` — don't run it here.

## 1. Identify the caller and the source

- **Caller identity** — handle via `gh api user --jq .login`; name/email via
  `git config user.name` / `user.email`. Show what you found and confirm; these
  values replace the original author's everywhere.
- **Source imprint** — the repo this skill shipped from (default: `gkurt/imprint`).
  Accept an override if the caller wants to base theirs on someone else's.

## 2. Ask where their repos live

To fill the fork with real preferences you need to read the caller's existing
repos. Ask where to look:

> Where do your repositories live? (e.g. `~/Work`, `~/dev`) — I'll survey a few to
> learn your conventions.

Offer the likely candidates first: the parent of the current repo, then common
roots (`~/dev`, `~/src`, `~/Work`, `~/Projects`, `~/code`, `~/git`). If none is
evident, ask outright. If they have no local repos, fall back to their GitHub
(`gh repo list <handle> --limit 100`) or offer to fill preferences interactively.

## 3. Learn their preferences

Survey a representative sample — prefer recent, active, non-fork repos. For each
category, record the actual values and note what's consistent across repos vs a
one-off:

- runtime & package manager, `tsconfig`, lint/format config, test setup, build
  tooling, CI workflows, release automation, `AGENTS.md`/`CLAUDE.md`, IDE
  settings, `.gitignore`, go-to libraries, license + author identity.

Templatize the consistent choices; skip repos with no signal. When repos
genuinely conflict, prefer the newest/most-consistent and ask only when it's a
real split.

## 4. Create the fork

Ask (or infer) which shape the caller wants, and where to put it — reuse the
repo location from step 2:

- **True fork** (keeps an upstream link so they can pull future improvements):
  ```bash
  gh repo fork <source> --clone --fork-name imprint   # add --org / a target dir as needed
  ```
- **Detached copy** (clean history, no fork relationship): clone the source,
  `rm -rf .git`, `git init`, then `gh repo create <handle>/imprint --private --source=. --push`.

Default to a true fork. Respect their public/private choice (private is fine —
the skill can read private imprints via `gh`).

## 5. Repopulate with their preferences

Replace the source templates with the caller's conventions, folder by folder
(same layout as `IMPRINT.md`):

- `config/` — their `tsconfig`, linter/formatter config, `editorconfig`,
  `gitignore`, `package.template.json`, git hooks.
- `stack/README.md` — rewrite with their stack and library defaults.
- `agents/` — their `AGENTS.md` rules; keep the `CLAUDE.md` → `@AGENTS.md`
  pointer only if they use that convention.
- `ci/` — their workflows and release tooling.
- `ide/` — their `.vscode` settings/extensions.
- `github/` — their repo-settings checklist.

Keep `IMPRINT.md` as the manifest and `skills/imprint/` as the entry point.
Update `IMPRINT.md`'s file mappings if they reorganize folders.

## 6. Scrub the original author's identity

Replace **every nominal reference** to the source author with the caller's. Grep
the whole fork for the original handle, name, email, and domain and fix each hit:

- Identity strings: GitHub handle, display name, emails, personal URL/domain
  (for `gkurt/imprint` that's `gkurt`, `KurtGokhan`, `Gökhan Kurt`, `Gokhan Kurt`,
  `gokhan@gkurt.com`, `krtgkn@gmail.com`, `gkurt.com`).
- `LICENSE` copyright holder (keep MIT or switch to their license of choice).
- README identity and any `homepage`/`repository`/`bugs` URLs.
- `config/package.template.json` author block and URLs.
- In `skills/imprint/SKILL.md`: change the **default fallback owner** from the
  source author to the caller's handle (so anyone who later uses *their* fork
  without their own imprint defaults to them), and reword the parenthetical that
  described it as "the author's." Update the `metadata` repo name/url too.

Be surgical: only touch **ownership/identity**, never the generic skill logic. If
it's a true fork, ask whether to keep a one-line attribution in the README
("Forked from `<source>`") — that's a legitimate reference, not one to scrub.

## 7. Verify, push, hand off

- Grep once more to confirm no stray original-author identity remains (except any
  intentional attribution).
- Run the fork's own checks if applicable, commit, and push to `<handle>/imprint`.
- Report what you templatized from their repos and anything still needing their
  hand (values you couldn't infer). Remind them: once `<handle>/imprint` exists,
  the skill finds it automatically and imprints *their* conventions.
