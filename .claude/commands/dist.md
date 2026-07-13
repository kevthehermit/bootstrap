---
description: Build Bootstrap's CSS + JS dist and check bundle sizes against the budget
argument-hint: "[css|js]"
allowed-tools: Bash(npm run *)
---

Compile the distributable CSS and JS, then verify the output stays within the size budget.

Arguments: `$ARGUMENTS`
- `css` → build styles only (`npm run css`).
- `js` → build scripts only (`npm run js`).
- empty → build both (`npm run dist`).

## Steps

1. Build:
   - both: `npm run dist` (runs `css` and `js` in parallel)
   - css only: `npm run css` (compile → prefix → RTL → minify)
   - js only: `npm run js` (rollup compile → terser minify)
2. Check sizes: `npm run bundlewatch` — compares files in `dist/` against the max sizes
   in `.bundlewatch.config.json`.

## Report

Report whether the build succeeded and the bundlewatch result. If any bundle is **over
budget**, list each offending file with its actual vs. max size and note that shrinking it
(or, deliberately, raising the limit in `.bundlewatch.config.json`) is required before the
change can land. Don't edit the budget yourself unless the user asks — flag it instead.
