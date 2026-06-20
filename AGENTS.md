# AGENTS.md

## Cursor Cloud specific instructions

### What this is
A single-page, static web app (`index.html`) that compiles a `UserGame.ini` for self-hosting a *Dune: Awakening* server via the [RedBlink dune-awakening-selfhost-docker](https://github.com/Red-Blink/dune-awakening-selfhost-docker) stack. The entire app — markup, styles, and vanilla JS — lives inline in `index.html`. There is **no build step, no package manager, and no backend**.

### Running it (dev)
Serve the repo root with any static file server and open the page, e.g.:

```
python3 -m http.server 8000   # then open http://localhost:8000/index.html
```

Changes are picked up by simply reloading the page — there is nothing to rebuild.

### Styling caveat
Tailwind is loaded from `https://cdn.tailwindcss.com`. With no internet the page still works functionally but renders unstyled, and the console logs a harmless `cdn.tailwindcss.com should not be used in production` warning plus a `favicon.ico` 404. Neither affects functionality.

### Lint / test / build
There is no linter, test suite, or build configured. To validate the inline JS logic headlessly (without a browser), load `index.html` with `jsdom` and drive the global functions (e.g. `addInstance`, `applyPreset`) / inspect `#partitionOutput`. Note: under jsdom the Tailwind CDN `<script>` does not load, so an inline `tailwind is not defined` error is expected and irrelevant.

### Domain notes (RedBlink partition model)
- The generated file is a single `UserGame.ini` containing one or more `[Partition:<Map>:<Partition#>:<Section>]` blocks — the RedBlink console treats `UserGame.ini` as the unified master file for all maps/partitions.
- Partition IDs are `Map:Partition#` (e.g. Hagga Basin hub `Survival_1:1`, Deep Desert PvE `DeepDesert_1:33`, Deep Desert PvP `DeepDesert_1:8`). The number is the **Partition #** shown on the dashboard's *Maps* page (a sietch listed as "Partition 44 / Dimension 1" → `Survival_1:44`); these numbers are server-assigned, not a simple 1,2,3 sequence.
- Multiple Hagga Basin instances are additional `Survival_1` partitions (child sietches), not separate map names.
