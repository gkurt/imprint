# IDE settings

Copy both into the repo's `.vscode/` directory.

- [`extensions.json`](extensions.json) → `.vscode/extensions.json` — recommends Biome + EditorConfig + **TypeScript (Native Preview)** (`TypeScriptTeam.native-preview`), which is the extension that speaks to the TypeScript 7 native language server. Nothing else; Biome replaces ESLint **and** Prettier.
- [`settings.json`](settings.json) → `.vscode/settings.json` — format-on-save via Biome, organize-imports on save, single-quote preference, and pins the workspace TypeScript version.

## TypeScript 7 in the editor

The bundled VS Code TypeScript extension still runs the old JS `tsserver`. To get
the native (Go) server that matches the TS7 `tsc` we typecheck with, install
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
