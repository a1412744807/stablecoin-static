## Purpose
This project is a small static dashboard site for stablecoin yields with standalone tutorial pages. The goal for an AI coding agent is to make safe, minimal, and consistent edits to the static assets, JavaScript logic, and tutorial content.

## Big picture
- **Type:** Static site served as Cloudflare Workers/Assets (see `wrangler.jsonc`).
- **Data flow:** Client-side JavaScript in `index.html` fetches a Google Sheets CSV (SHEET_ID constant) through CORS proxies, parses rows into objects, caches them in `localStorage`, and renders the table and tutorial links.
- **Content areas:** UI and logic are contained in `index.html`; tutorials live under `tutorials/` using `tutorials/template.html` as an example; images are stored in `images/tutorials/<slug>/`.

## Developer workflows (discoverable)
- **Preview locally:** open `index.html` directly in a browser or run a simple server: `python3 -m http.server 8080` from the repo root and visit `http://localhost:8080/`.
- **Cloudflare publish:** this repo includes `wrangler.jsonc` with `assets.directory: "./"` — use `wrangler publish` (Wrangler must be installed and authenticated) to publish static assets to Cloudflare.

## Project-specific patterns & conventions
- **Single-file app:** Most JS lives inline in `index.html`. Make small JavaScript fixes in-place unless a user requests refactoring to separate files.
- **CSV schema:** Column names are mapped in the `COL` constant inside `index.html` — modify UI behavior by editing that mapping or the CSV headers. The code expects headers like `平台`, `币种`, `年化（APY）`, `教程链接`, etc.
- **CORS & proxies:** The app attempts multiple proxies (`CORS_PROXIES`) before falling back to the direct URL. If adding network logic, preserve the proxy fallback order.
- **Caching keys:** `localStorage` keys used: `stablecoin_dashboard_data` and `stablecoin_dashboard_time`. Keep changes compatible with existing cache format (JSON array of rows).
- **Tutorials:** Add a new tutorial page by copying `tutorials/template.html` to `tutorials/<slug>.html` and corresponding images to `images/tutorials/<slug>/`. CSV `教程链接` should point to `tutorials/<slug>.html` (relative path works).
- **i18n/Language:** UI and content are Chinese (zh-CN). Keep new copy/content in this language unless instructed otherwise.

## Safety & deployment notes
- Do not change the `SHEET_ID` unless you know the data source. If changing it, update the `DIRECT_URL` and test the CSV parsing locally.
- When altering time-parsing or APY parsing logic, add conservative fallbacks; malformed CSV rows are common and the app currently ignores empty platform rows.

## Examples (code pointers)
- Update CORS proxies: edit `CORS_PROXIES` in [index.html](index.html#L64).
- Change cache duration: `CACHE_DURATION` in [index.html](index.html#L71).
- CSV column mapping: `COL` constant in [index.html](index.html#L106).
- Tutorial template: see [tutorials/template.html](tutorials/template.html#L1).

## What to ask the maintainer before larger changes
- Intended hosting (Cloudflare Pages vs Workers Sites) and Wrangler credentials for publishing.
- Whether to refactor inline JS into separate modules — small bugfixes should remain inline to minimize risk.

If anything in this summary is unclear or you'd like me to expand a section (e.g., add exact `wrangler` commands or split the inline JS into files), tell me which part to update.
