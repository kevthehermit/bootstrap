---
name: visual-check
description: Visually verify Bootstrap docs/components in a real browser using the Playwright MCP server — boots the Astro docs dev server, navigates to a component or example page, and screenshots it in light/dark and at mobile/desktop widths. Use when asked to "screenshot", "preview", "visually check/verify", or "see how <component> looks" after a CSS/JS change.
---

# Visual check of the Bootstrap docs

Drives the live docs site in a headless browser to confirm a change actually renders,
rather than trusting the diff. Uses the `playwright` MCP server defined in `.mcp.json`.

## Prerequisites (one-time)

The Playwright browser binary must be installed once (the Python package is fetched
automatically by `uv`):

```bash
uv run --with playwright playwright install chromium
```

If a `navigate`/`screenshot` tool call fails with a "Could not launch Chromium"
message, run the command above and retry.

## Steps

1. **Start the docs dev server** if it isn't already running. It's slow to boot the
   first time (Astro builds the whole site), so run it in the background and wait for
   the "watching for file changes" / local URL line before navigating:

   ```bash
   npm run docs-serve      # astro dev --root site --port 9001
   ```

   Run this with the Bash tool's `run_in_background` option; poll the output until the
   server is ready. If you changed `scss/` or `js/src/`, rebuild dist first with the
   `/dist` command (or `npm run css`), since the docs load compiled assets.

2. **Navigate** with the `playwright` MCP `navigate` tool. Component pages live at:

   ```
   http://localhost:9001/docs/5.3/components/<name>/      # e.g. .../components/alert/
   http://localhost:9001/docs/5.3/examples/               # example gallery
   http://localhost:9001/docs/5.3/                        # docs home
   ```

3. **Screenshot** the states that matter for the change. Use the MCP tools:
   - `screenshot` — capture the page (default full-page PNG).
   - `set_theme("dark")` / `set_theme("light")` — toggle Bootstrap's `data-bs-theme`;
     re-screenshot to check both color modes.
   - `set_viewport(375, 667)` then re-screenshot to check the mobile breakpoint;
     `set_viewport(1280, 900)` to return to desktop.
   - `click(selector)` — exercise interactive components (open a modal, dismiss an
     alert, expand an accordion) before screenshotting.

   Save screenshots to the scratchpad directory unless the user wants them elsewhere.

4. **Read the screenshots back** (the Read tool renders PNGs) and describe what you see:
   does the component render correctly, in both themes, at both widths? Call out
   anything broken, misaligned, or missing.

5. **Clean up**: close the browser with the `close` MCP tool and stop the background
   docs server when done.

## Notes

- The dev server serves the docs, which reference `dist/css` + `dist/js`. A pure `scss`
  edit won't be visible until `npm run css` (or `watch`) regenerates `dist/`.
- For a specific component you just built, combine with the `new-component` skill and
  the `/dist` command first, then visually check the new `components/<name>/` page.
