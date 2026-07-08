# IDE settings

Copy both into the repo's `.vscode/` directory.

- [`extensions.json`](extensions.json) → `.vscode/extensions.json` — recommends Biome + EditorConfig. Nothing else; Biome replaces ESLint **and** Prettier.
- [`settings.json`](settings.json) → `.vscode/settings.json` — format-on-save via Biome, organize-imports on save, single-quote preference, and pins the workspace TypeScript version.

Per-language additions you might layer on:

- **Astro repos**: add an `"[astro]": { "editor.defaultFormatter": "astro-build.astro-vscode" }` override and recommend `astro-build.astro-vscode`.
- **Tailwind repos**: `"files.associations": { "*.css": "tailwindcss" }` and recommend `bradlc.vscode-tailwindcss`.
- **Bun test explorer**: `"bun.test.enable": true`.
