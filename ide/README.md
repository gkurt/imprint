# IDE settings

Copy both into the repo's `.vscode/` directory.

- [`extensions.json`](extensions.json) → `.vscode/extensions.json` — recommends Oxc (`oxc.oxc-vscode`) + EditorConfig + **TypeScript (Native Preview)** (`TypeScriptTeam.native-preview`), which is the extension that speaks to the TypeScript 7 native language server. Nothing else; the Oxc extension runs Oxlint, replacing ESLint.
- [`settings.json`](settings.json) → `.vscode/settings.json` — Oxlint fixes on save, single-quote preference, and pins the workspace TypeScript version. Format-on-save is off: `bun fmt` has no language server or editor extension, so formatting happens in `bun run fix` and the pre-commit hook (`bun fmt --staged`). It's set to `false` explicitly so a user-level setting doesn't hand the file to VS Code's built-in formatter, which disagrees with `bun fmt`.

## TypeScript 7 in the editor

The bundled VS Code TypeScript extension still runs the old JS `tsserver`. To get
the native (Go) server that matches the TS7 checker `bun check` follows, install
`TypeScriptTeam.native-preview` and turn it on:

- `"js/ts.experimental.useTsgo": true` — routes IntelliSense to the native server.
  (Equivalent to running **TypeScript 7: Enable TypeScript 7 Language Server**.)
- `"js/ts.tsdk.path": "node_modules/typescript/lib"` — use the workspace's own
  TypeScript rather than whatever the extension bundles.

Note the `js/ts.*` namespace: the TS7 settings live there, not under `typescript.*`.
`typescript.native-preview.*` and `typescript.tsdk` are the deprecated spellings.

Per-language additions you might layer on:

- **Astro repos**: add an `"[astro]": { "editor.defaultFormatter": "astro-build.astro-vscode" }` override and recommend `astro-build.astro-vscode`.
- **Tailwind repos**: `"files.associations": { "*.css": "tailwindcss" }` and recommend `bradlc.vscode-tailwindcss`.
- **Bun test explorer**: `"bun.test.enable": true`.
