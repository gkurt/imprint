# Git

Release notes: https://github.com/git/git/tree/master/Documentation/RelNotes ·
Highlights: https://github.blog/open-source/git/

Newest first. `git history` and `git replay` are experimental, so check their `-h` output
every time.

## 2.56 (2026-09) · [RN](https://github.com/git/git/blob/master/Documentation/RelNotes/2.56.0.adoc) · [Highlights](https://github.blog/open-source/git/highlights-from-git-2-56/)

| Feature | Check |
|---|---|
| `git add --resolved`: stages only the paths that had conflicts, and aborts if any conflict markers are left | `git add -h` |
| `git history drop <commit>`: removes a commit and replays its descendants | `git history -h` |
| `git branch --delete-merged`: deletes local branches already merged into their upstream | `git branch -h` |
| `git bisect ... --reset-when-found[=<where>]` | `git bisect -h` |
| `git refs create/delete/update/rename` | `git refs -h` |
| `git replay --linearize`: drops merge commits, like `rebase --no-rebase-merges` | `git replay -h` |
| `[includeIf]` conditions can match the worktree location | `git help config` |
| `fetch.followRemoteHEAD`: default for the per-remote `remote.<name>.followRemoteHEAD` | `git help config` |

## 2.55 (2026-06) · [RN](https://github.com/git/git/blob/master/Documentation/RelNotes/2.55.0.adoc) · [Highlights](https://github.blog/open-source/git/highlights-from-git-2-55/)

| Feature | Check |
|---|---|
| `git history fixup`: folds staged changes into an earlier commit | `git history -h` |
| Hooks defined in config can run in parallel | `git help hooks` |
| `git push <remote-group>`: pushes to several remotes, like `git fetch` does | `git help push` |
| `git checkout -m <branch>` saves local changes in a stash, so a failed conflict resolution can be retried | `git help checkout` |
| `git format-rev`: pretty-prints revisions, or object names found in text | `git format-rev -h` |
| `git log --max-count-oldest=<n>`; `--graph` output can be limited to a number of lanes | `git help log` |
| fsmonitor daemon on Linux | `git help fsmonitor--daemon` |
| Terminal control sequences sent by remotes are blocked by default (colours still allowed) | — |

## 2.54 (2026-04) · [RN](https://github.com/git/git/blob/master/Documentation/RelNotes/2.54.0.adoc) · [Highlights](https://github.blog/open-source/git/highlights-from-git-2-54/)

| Feature | Check |
|---|---|
| `git history reword\|split <commit> [--update-refs=branches\|head]`: new experimental history-rewriting command; `--update-refs` also moves dependent branches | `git history -h` |
| Hooks can be defined in config, with several commands per event | `git help hooks` |
| `git status` compares the current branch with the branches in `status.compareBranches` | `git help config` |
| `git rebase --trailer` | `git rebase -h` |
| `git add -p` can return to a file already handled | `git help add` |
| `git replay`: drops commits that become empty, adds a `revert` mode, can replay down to the root | `git replay -h` |
| `git backfill` accepts revisions and pathspecs | `git backfill -h` |
