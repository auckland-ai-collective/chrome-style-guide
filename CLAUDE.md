# Project: Chrome Style Guide

This project defines the window-shell design system for an Electron app (title bar,
tab strip, toolbar, omnibar, menus, overlays). It is a **design/reference project**,
not the app itself.

## Source of truth
- **`STYLE_GUIDE.md`** — authoritative text spec (tokens, per-component px measurements,
  states, behavior). Read this before implementing or changing any component.
- **`Chrome Style Guide.dc.html`** — the annotated visual style guide (embeds the live window).
- **`Chrome Style Guide Window.dc.html`** — the interactive window mockup (the reusable reference).
- **`Chrome Style Guide Menu.dc.html`** — the reusable app-menu component.
- **`Chrome Style Guide.html`** — the exported standalone (offline) bundle. Do not edit by
  hand; regenerate from the `.dc.html` sources.

If a measurement in `STYLE_GUIDE.md` and the rendered `.dc.html` ever disagree, the
**HTML is the source of truth** — fix the markdown to match, not the reverse.

## Working rules
- These are `.dc.html` Design Components. Edit them with the DC tools; the window and
  menu are imported by name (`Chrome Style Guide Window`, `Chrome Style Guide Menu`) —
  keep those import names in sync with the filenames if you rename anything.
- Preserve the shared **23px tab-strip centerline**: any control added to the tab strip
  must be vertically centered with the tabs (see STYLE_GUIDE §4).
- Dark theme, Windows-style window controls, `Segoe UI / system-ui` font, accent `#2f6fd6`.
- Do not give this work a separate product name. Refer to it as the "Chrome Style Guide".
  `sygnal.com` may appear only as the real org domain (e.g. a "managed by" line), never as
  a product name.
- Regenerate `Chrome Style Guide.html` after changing any source component.
