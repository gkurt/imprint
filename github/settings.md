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

## Actions & publishing

- **Workflow permissions**: allow GitHub Actions to create and approve pull
  requests, or the release workflow can't open Tegami's "Version Packages" PR.
  Keep the default token read-only; each workflow asks for the write
  permissions it needs. Agents: set it with `gh` (in the UI: Settings → Actions
  → General → Workflow permissions), then read it back to confirm:

  ```bash
  gh api -X PUT repos/<owner>/<repo>/actions/permissions/workflow \
    -f default_workflow_permissions=read -F can_approve_pull_request_reviews=true
  gh api repos/<owner>/<repo>/actions/permissions/workflow
  # {"default_workflow_permissions":"read","can_approve_pull_request_reviews":true}
  ```

  In an organization, the org's Actions settings must allow it first. If the
  call is refused, report it as one for the user's hand.
- **npm trusted publishing**: on npmjs.com, configure the package to trust this
  repo's `release.yml`. No `NPM_TOKEN` secret needed — `id-token: write` handles OIDC.
- For docs sites: Settings → Pages → source = GitHub Actions.
