---
name: imprint
description: Set up a repository with a person's standing preferences — configs, lint/format, tsconfig, CI, GitHub settings, AGENTS.md, IDE settings. Use when the user wants to "imprint" a repo, scaffold a new project to their conventions, apply their house style, or bootstrap tooling for a fresh or existing repo. Looks for a `<username>/imprint` repo and applies its IMPRINT.md. Pass `fork` to instead create the caller's own imprint repo from this one.
user-invocable: true
disable-model-invocation: true
argument-hint: "[fork]"
license: MIT
metadata:
  - type: repo
    name: gkurt/imprint
    url: https://github.com/gkurt/imprint
---

# Imprint

Apply a person's standing repository preferences to the current project. Those
preferences live in a public `<username>/imprint` repo — a manifest (`IMPRINT.md`)
plus template files for configs, CI, IDE settings, and agent instructions.

"To imprint" = read that manifest and apply it to the working repo, adapting to
what the repo already is rather than blindly overwriting.

## Modes

- **Default (no argument)** — apply an imprint to the current repo. Follow the
  procedure below.
- **`fork`** — create the caller's *own* imprint repo from this one, populated
  with their preferences. Do **not** follow the procedure below; follow
  [fork.md](fork.md) instead.

## Procedure

### 1. Resolve the imprint source

A source is a **local directory** containing `IMPRINT.md`, or a **GitHub repo**
(public or private). If the user named one ("imprint from `~/dev/imprint`", or
"from `torvalds`"), use it and skip detection. Otherwise check, cheapest first:

1. **Local clone**: `$IMPRINT_DIR`, a sibling `../imprint`, then `~/imprint`,
   `~/dev/imprint`, `~/Work/**/imprint`, `~/src/imprint`.
2. **The user's GitHub handle**: `gh api user --jq .login`, else the owner in
   `git config --get remote.origin.url`, else `git config user.name` as a hint.
   Then `gh repo view <handle>/imprint --json name,visibility`. `gh` sees
   private repos too.

Tell the user which source you picked and whether it's local, private, or public.

**A local clone must be fresh.** Run `git -C <dir> fetch`, then
`git -C <dir> rev-list --count HEAD..@{u}`. Use it silently only when it is
verifiably up to date with its upstream. If it's behind, has uncommitted edits,
or freshness can't be checked (no remote, offline), ask:

> Your local imprint at `<dir>` is N commits behind origin (or: has local
> changes / can't verify it's current). Update it, use it as-is, or fetch from
> GitHub instead?

"Update" means `git -C <dir> pull --ff-only`.

**If nothing is found**, ask:

> No imprint found for you (local or on GitHub). Imprint from someone else's, or
> a local path? (default: `gkurt`)

Accept a local path, any `owner/imprint`, or a bare `owner`. If they just
confirm, use **`gkurt`** (the author's, the one this skill shipped from), and
suggest they fork it for next time (`/imprint fork`).

### 2. Read the source

Read a local source directly. For a GitHub source, go through `gh` so private
repos work:

```bash
gh api repos/<owner>/imprint/contents/IMPRINT.md --jq .content | base64 -d
gh repo clone <owner>/imprint <tmp>/imprint -- --depth 1   # when you need many files
```

Without `gh`, a public repo can fall back to `raw.githubusercontent.com` or an
HTTPS clone. For a private repo `gh` can't reach, stop and ask the user to
`gh auth login`, clone it locally, or give a local path.

The skill is content-free by design: always read the live source. Never
fabricate imprint content or fall back to a remembered stack.

### 3. Apply IMPRINT.md, adapting rather than clobbering

`IMPRINT.md` is the source of truth: the order to apply things, the
source→destination file mapping, and rename rules (`gitignore` → `.gitignore`).
Read `stack/README.md` first to decide the archetype.

- **Detect the target repo first**: language, existing configs, monorepo or not,
  package manager. Apply only what fits.
- **Merge, don't overwrite.** Merge `package.json` scripts into the existing
  file. For an existing config that differs meaningfully, show the diff and
  confirm before replacing.
- **Fill placeholders** (`PACKAGE_NAME`, `PACKAGE_DESCRIPTION`, the `username`
  in URLs and author blocks) with the target repo's values and the current
  user's identity, not the imprint owner's.

### 4. Verify and hand off

- Install deps and run the aggregate check (`bun run checks`). Fix what
  lint/format flags.
- Summarize what you copied, merged, and skipped (and why).
- Flag what needs the user's hands: npm trusted publishing, branch protection
  that needs the GitHub UI, secrets.
