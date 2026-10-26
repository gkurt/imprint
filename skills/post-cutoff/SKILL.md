---
name: post-cutoff
description: Index of git, GitHub CLI (gh) and GitHub platform features released from April 2026 on — commands, flags and workflows a model trained earlier will not know exist. Read before running git or gh commands, opening or editing pull requests and issues, rewriting history, or choosing a branching, review or merge workflow; and whenever the user names a git, gh or GitHub feature you do not recognize. It only says what exists and where it is documented; it defers to official docs and official skills for how to use it.
---

# Post-cutoff index

An index, not a manual. Each entry says that a feature exists, when it shipped, how to
confirm it locally, and where the authoritative explanation lives. Learn the details from
that source, not from this file.

Last reviewed: **2026-10-26**. For anything newer, read the changelogs listed at the top of
each reference file.

## Reference files

Open only the file for the tool you are about to use.

| File | Covers |
|---|---|
| `references/gh.md` | GitHub CLI and official `gh` extensions |
| `references/git.md` | Git |
| `references/github.md` | github.com platform features |

## How to use an entry

1. **Skip entries older than your training cutoff.** You already know them.
2. **Released does not mean installed.** Compare the entry's version with `gh --version` or
   `git --version`. If the installed one is older, tell the user the feature needs an
   upgrade. Do not improvise a workaround that imitates it.
3. **Run the Check command.** Local `--help` output is authoritative for the installed
   version and wins over this index when they disagree.
4. **Use the official skill when an entry lists one.** If it is installed, load it and
   follow it. If it is not, give the user its install command; do not install it unasked.
5. **Read the Docs link before relying on details** such as flag semantics, limits and edge
   cases. Do not fill gaps from memory.
6. **Preview and experimental features change without notice.** Re-check `--help` rather
   than trusting this index or an earlier session.

## Maintaining this index

- One row per feature: date, version, a clause naming what it does, a check, a primary source.
- Add only features that change which command or workflow you would choose. Leave out
  performance work, internals and bug fixes.
- Sources are release notes, changelog posts and official docs. Secondary coverage is
  allowed only when marked `(unconfirmed)`.
- Delete rows once every model in use was trained after them. Keep each file under ~60 rows.
