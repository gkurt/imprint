# CI & release workflows

Drop these into `.github/workflows/`. All jobs run on Bun via `oven-sh/setup-bun@v2`, and actions are pinned to current majors (`checkout@v6`, `setup-node@v6`).

| File | Trigger | Purpose |
| --- | --- | --- |
| [`ci.yml`](ci.yml) | push / PR to `main`, manual | Lint + test + typecheck (add a build step for publishable packages). |
| [`release.yml`](release.yml) | push to `main` | [Tegami](https://tegami.fuma-nama.dev): opens a "Version Packages" PR when changelogs are pending, otherwise publishes. Uses npm trusted publishing (OIDC) — no `NPM_TOKEN`. |
| [`tegami-pr.yml`](tegami-pr.yml) | PR | Builds a release-preview artifact. Runs untrusted PR code but stays read-only. |
| [`tegami-pr-comment.yml`](tegami-pr-comment.yml) | `workflow_run` of the above | Posts the preview as a PR comment with write perms, without running PR code. |

## Notes

- **Release tooling is Tegami**, the successor to Changesets. Config lives in `scripts/tegami.mts` (all packages share one version via `groups: { all: { syncBump: true } }`, `npm.client: 'bun'`, `github()` plugin). If you prefer Changesets, `/migrate-from-changesets` goes the other way — but the house default is Tegami.
- **npm trusted publishing**: configure the package on npmjs.com to trust this repo's `release.yml`. The `id-token: write` permission is what enables OIDC; no secret token is stored.
- The **PR-preview split** (`tegami-pr.yml` + `tegami-pr-comment.yml`) is deliberate: the workflow that runs untrusted PR code has read-only perms; the one with write perms never runs PR code. Keep them paired.
- For sites, add a `deploy-docs.yml` that builds the Astro docs and deploys to GitHub Pages (`actions/deploy-pages`, `concurrency.group: pages`).
