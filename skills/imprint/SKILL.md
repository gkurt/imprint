---
name: imprint
description: Set up a repository with a person's standing preferences — configs, lint/format, tsconfig, CI, GitHub settings, AGENTS.md, IDE settings. Use when the user wants to "imprint" a repo, scaffold a new project to their conventions, apply their house style, or bootstrap tooling for a fresh or existing repo. Looks for a `<username>/imprint` repo and applies its IMPRINT.md; defaults to gkurt's if the user has none.
user-invocable: true
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

## Procedure

### 1. Pick whose imprint to apply

Determine the user's own GitHub handle, in this order:

1. `gh api user --jq .login` (if `gh` is authenticated).
2. Parse `git config --get remote.origin.url` for `github.com[:/]<owner>/`.
3. `git config user.name` as a last hint.

Then check whether that user has their own imprint repo:

```bash
gh repo view <handle>/imprint --json name 2>/dev/null
```

- **If `<handle>/imprint` exists** → use it. Tell the user you found their imprint.
- **If it does not exist** → ask:

  > No `<handle>/imprint` repo found. Imprint from someone else's? (default: `gkurt`)

  Accept any `owner/imprint` or bare `owner`. If they just confirm, use **`gkurt`**
  (the author's — the one this skill shipped from). Encourage them to fork it and
  make their own for next time (see the source repo's README).

### 2. Fetch the manifest

Prefer read-only fetching over a full clone. Read the chosen repo's `IMPRINT.md`:

```bash
# Raw file (no clone):
gh api repos/<owner>/imprint/contents/IMPRINT.md --jq .content | base64 -d
```

If you need many files, clone shallowly into a temp dir instead:

```bash
git clone --depth 1 https://github.com/<owner>/imprint <tmp>/imprint
```

Do NOT assume the imprint content is bundled with this skill — always fetch it
live so you get the owner's current preferences.

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

- This skill is content-free by design — it points at a live `<username>/imprint`
  repo so preferences stay current and anyone can bring their own.
- If the user says a specific owner ("imprint from `torvalds`"), skip step 1's
  ownership check and go straight to that repo.
- If `gh` isn't available or authenticated, fall back to raw HTTPS
  (`https://raw.githubusercontent.com/<owner>/imprint/main/IMPRINT.md`) or a clone.
