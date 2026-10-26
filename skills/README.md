# Skills

Every skill here except `imprint` is installed into `.claude/skills/<name>/` in the
projects it fits (step 6 of [`IMPRINT.md`](../IMPRINT.md)). Adding a skill folder here
is enough for imprint to pick it up, as long as it has an entry below saying where it
applies.

- [`imprint/`](imprint/) — drives imprinting itself. Installed by users, never copied
  into projects.
- [`changelog/`](changelog/) → repos that release with Tegami. Teaches agents to write
  `.tegami/<slug>.md` entries.
- [`post-cutoff/`](post-cutoff/) → every repo. An index of git, `gh` and GitHub features
  newer than most models' training, pointing to official docs and skills. Re-copy it
  when it's updated here.
