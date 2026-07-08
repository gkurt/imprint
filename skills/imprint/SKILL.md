---
name: imprint
description: Set up a repository with a person's standing preferences — configs, lint/format, tsconfig, CI, GitHub settings, AGENTS.md, IDE settings. Use when the user wants to "imprint" a repo, scaffold a new project to their conventions, apply their house style, or bootstrap tooling for a fresh or existing repo. Looks for a `<username>/imprint` repo and applies its IMPRINT.md; defaults to gkurt's if the user has none. Pass `fork` to instead create the caller's own imprint repo from this one.
user-invocable: true
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

### 1. Pick whose imprint to apply

An imprint source can be **a local directory** or **a GitHub repo (public or
private)**. If the user named one explicitly ("imprint from `~/dev/imprint`", or
"from `torvalds`"), use that and skip the detection below.

Otherwise, resolve the user's own imprint, checking cheapest-first:

1. **Local clone** — the user may already have it on disk. Check, in order:
   - `$IMPRINT_DIR` if set.
   - Common spots near the current repo: a sibling `../imprint`, and dev roots
     like `~/imprint`, `~/dev/imprint`, `~/Work/**/imprint`, `~/src/imprint`.
   - A directory is a valid imprint if it contains an `IMPRINT.md`.
2. **Their GitHub handle**, via `gh api user --jq .login` (if `gh` is
   authenticated), else parse `git config --get remote.origin.url` for
   `github.com[:/]<owner>/`, else `git config user.name` as a hint. Then:
   ```bash
   gh repo view <handle>/imprint --json name,visibility 2>/dev/null
   ```
   `gh` sees **private** repos the user can access, so this works either way.

- **If a local clone or `<handle>/imprint` is found** → use it. Tell the user
  which source (and whether it's local / private / public). For a **local clone
  with a git remote**, check it isn't stale before trusting it: `git -C <dir>
  fetch` then compare with `git -C <dir> status -sb` / `git -C <dir> rev-list
  --count HEAD..@{u}`. If it's behind its upstream (or has uncommitted local
  edits, or you can't determine freshness — no remote, offline), **ask**:

  > Your local imprint at `<dir>` is N commits behind origin (or: has local
  > changes / can't verify it's current). Update it, use it as-is, or fetch from
  > GitHub instead?

  Only skip the prompt when the clone is verifiably up to date with its upstream.
- **If none is found** → ask:

  > No imprint found for you (local or on GitHub). Imprint from someone else's, or
  > a local path? (default: `gkurt`)

  Accept a local path, any `owner/imprint`, or a bare `owner`. If they just
  confirm, use **`gkurt`** (the author's — the one this skill shipped from).
  Encourage them to fork it and make their own for next time (see the source
  repo's README).

### 2. Read the manifest from the source

**If the source is a local directory**, read the files directly — no clone
needed. Prefer a local clone whenever one exists, but only after the freshness
check in step 1: a local clone is the fast path, not an excuse to apply stale
preferences. If the user chose "update," pull first (`git -C <dir> pull --ff-only`)
and then read; if they chose "fetch from GitHub instead," treat it as a remote
source below.

**If the source is a GitHub repo**, get its `IMPRINT.md` in a way that works for
private repos too:

```bash
# gh API — authenticated, so it works for private AND public repos:
gh api repos/<owner>/imprint/contents/IMPRINT.md --jq .content | base64 -d
```

If you'll need many files, clone shallowly (SSH or gh handles private auth;
`git clone https://…` and `raw.githubusercontent.com` do NOT work for private
repos without a token — don't rely on them):

```bash
gh repo clone <owner>/imprint <tmp>/imprint -- --depth 1
```

If `gh` is unavailable/unauthenticated and the repo is public, fall back to
`https://raw.githubusercontent.com/<owner>/imprint/main/IMPRINT.md` or an HTTPS
clone. If it's private and `gh` can't reach it, stop and ask the user to either
`gh auth login`, clone it locally, or point you at a local path — do not guess.

Do NOT assume the imprint content is bundled with this skill — always read it
from the resolved source so you get the owner's current preferences.

### 3. Follow IMPRINT.md

`IMPRINT.md` is the source of truth. It lists the folders (`stack/`, `config/`,
`ide/`, `agents/`, `ci/`, `github/`) and the order to apply them, including the
source→destination file mapping and any rename rules (e.g. `gitignore` → `.gitignore`).

Read `stack/README.md` first to fix the archetype (Bun monorepo? Astro site? npm
lib?), then apply each section.

### 4. Apply — adapt, don't clobber

- **Detect the target repo's reality first**: language, existing configs, whether
  it's a monorepo, what package manager it uses. Only apply what fits.
- **Merge, don't overwrite blindly.** For `package.json`, merge scripts and
  devDependencies into the existing file rather than replacing it. For configs
  that already exist and differ meaningfully, show the diff and confirm before
  replacing.
- **Fill placeholders** (`PACKAGE_NAME`, `PACKAGE_DESCRIPTION`, the `gkurt` owner
  in URLs/author blocks) with the target repo's real values and the current user's
  identity — not the imprint owner's, unless they're the same person.
- Rename dotless template files on copy as `IMPRINT.md` specifies.

### 5. Verify and hand off

- Install deps (`bun i`, or the repo's manager) and run the aggregate check
  (`bun run checks`). Fix what lint/format flags.
- Report a summary: what you copied, what you merged, what you skipped and why.
- Flag anything that needs the user's hands: npm trusted-publishing setup, branch
  protection that needs the GitHub UI, secrets.

## Notes

- This skill is content-free by design — it reads a live imprint source (local
  dir or GitHub repo) so preferences stay current and anyone can bring their own.
- An imprint source can be **local, a private repo, or a public repo.** Prefer a
  local clone when one exists — but verify it isn't stale first and ask if it is;
  use `gh` (not raw HTTPS) for private repos.
- If the user names a specific source ("imprint from `torvalds`", or a path),
  skip the detection and use it directly.
- Never fabricate imprint content or fall back to a hardcoded stack if you can't
  reach the source — surface the access problem and ask how to proceed.
