# GitHub CLI (`gh`)

Changelog: https://github.com/cli/cli/releases · Manual: https://cli.github.com/manual

Newest first. `RN` links go to the release notes for that version.

| Date | Version | Feature | Check | Docs |
|---|---|---|---|---|
| 2026-10-21 | 2.103.0 | `gh auth login --expiring`: short-lived OAuth tokens that refresh automatically (preview). Read `gh auth token` fresh each time; do not cache it. | `gh auth login --help` | [RN](https://github.com/cli/cli/releases/tag/v2.103.0) |
| 2026-10-21 | 2.103.0 | `gh issue artifact list/view/create/edit/download/delete`: documents attached to issues (private preview). | `gh issue artifact --help` | [RN](https://github.com/cli/cli/releases/tag/v2.103.0) |
| 2026-09-15 | 2.101.0 | `gh auth login` copies the device code to the clipboard; `gh config set clipboard disabled` turns it off. | `gh config --help` | [RN](https://github.com/cli/cli/releases/tag/v2.101.0) |
| 2026-09-03 | 2.100.0 | Per-host `api_host` sends API traffic through a gateway (experimental). | `gh config --help` | [RN](https://github.com/cli/cli/releases/tag/v2.100.0) |
| 2026-09-03 | 2.100.0 | When a coding agent runs gh, misuse prints the full command help. | — | [RN](https://github.com/cli/cli/releases/tag/v2.100.0) |
| 2026-09-01 | 2.99.0 | `--attach <file>[#alt]` on `issue`/`pr` `create`/`edit`/`comment`: uploads images or video (video takes no alt text), up to 50 per command. | `gh pr create --help` | [Changelog](https://github.blog/changelog/2026-09-01-github-cli-media-in-issues-pull-requests-and-comments/) |
| 2026-09-01 | 2.99.0 | `gh issue develop --checkout --worktree <path>` | `gh issue develop --help` | [RN](https://github.com/cli/cli/releases/tag/v2.99.0) |
| 2026-08-20 | 2.98.0 | `gh pr checkout --worktree <path>` | `gh pr checkout --help` | [RN](https://github.com/cli/cli/releases/tag/v2.98.0) |
| 2026-08-20 | 2.98.0 | `gh search issues --search-type lexical\|semantic\|hybrid` | `gh search issues --help` | [RN](https://github.com/cli/cli/releases/tag/v2.98.0) |
| 2026-07-31 | 2.97.0 | `gh project item-edit --field <name> --value <v>` and `item-list --field <name>`: refer to project fields by name instead of ID. | `gh project item-edit --help` | [RN](https://github.com/cli/cli/releases/tag/v2.97.0) |
| 2026-06-17 | — | `gh repo read-file`, `gh repo read-dir`: read a repo without cloning it (preview). | `gh repo read-file --help` | [Changelog](https://github.blog/changelog/2026-06-17-read-remote-repository-content-with-github-cli/) |
| 2026-04-16 | — | `gh skill search/preview/install/update/list/publish`: agent skills hosted in GitHub repos (preview). | `gh skill --help` | [Changelog](https://github.blog/changelog/2026-04-16-manage-agent-skills-with-github-cli/) |

## Official extensions

| Date | Extension | Feature | Check | Docs / skill |
|---|---|---|---|---|
| 2026-04 | `github/gh-stack` | `gh stack`: stacked pull requests. Install with `gh extension install github/gh-stack`. | `gh stack --help` | **Official skill: `gh skill install github/gh-stack`** (defer to it). [README](https://github.com/github/gh-stack) · [Platform docs](https://docs.github.com/en/pull-requests/get-started/about-stacked-prs) |
