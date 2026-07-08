# GitHub repository settings

Apply these after creating the repo. Most can be set with `gh` (shown inline).

## Basics

- **Default branch**: `main`.
- **License**: MIT, copyright "User Name" (see the repo root `LICENSE`).
- **Description + homepage**: set the one-line description and `https://username.com/<project>/` if there's a docs site.
- **Topics**: add relevant topics (`bun`, `typescript`, `cli`, etc.).

```bash
gh repo edit --default-branch main \
  --description "..." --homepage "https://username.com/<project>/" \
  --add-topic bun --add-topic typescript
```

## Features

- **Disable Projects** — unused; keep the repo surface minimal.

```bash
gh repo edit --enable-projects=false
```

## Merge settings

- **Allow squash merging** — default. Disable merge commits and rebase merging.
- **Auto-delete head branches** after merge.
- **Always suggest updating pull request branches** — surfaces the "Update branch" button so PRs merge against a fresh `main`.
- Default squash commit message = PR title + description.

```bash
gh repo edit \
  --enable-squash-merge --enable-merge-commit=false --enable-rebase-merge=false \
  --delete-branch-on-merge --allow-update-branch
```

## Branch protection for `main`

- Require the **CI** status check (`checks`) to pass before merge.
- Require branches to be up to date before merging.
- Require a PR before merging (solo repos: keep required approvals at 0 so you can self-merge).
- Do **not** allow force pushes or deletions on `main`.

Note: `release.yml` pushes version-bump commits to `main`, so if you enable
"require PR before merge" strictly, allow the release GitHub App / token to bypass,
or rely on the Version Packages PR flow.

## Actions & publishing

- **Workflow permissions**: allow GitHub Actions to create and approve pull
  requests (Tegami opens the "Version Packages" PR). Settings → Actions → General.
- **npm trusted publishing**: on npmjs.com, configure the package to trust this
  repo's `release.yml`. No `NPM_TOKEN` secret needed — `id-token: write` handles OIDC.
- For docs sites: Settings → Pages → source = GitHub Actions.
