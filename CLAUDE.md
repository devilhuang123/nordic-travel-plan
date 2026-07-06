# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static travel-itinerary website for a 12-day Norway trip (2026/9/18–9/29, 2 adults), published via GitHub Pages. There is no build system, no package manager, no tests, and no framework — just five hand-authored HTML pages at the repo root. Content is written in Traditional Chinese (`lang="zh-TW"`).

## Development

- No build/lint/test commands exist. Edit the HTML files directly.
- Preview locally with any static server, e.g. `python3 -m http.server 8000`, or open the files in a browser.
- Deployment is GitHub Pages serving the `main` branch root. `.nojekyll` must remain in the repo root (it was added to fix stalled Pages deploys — do not delete it).
- Keep pages lightweight. The site was deliberately rebuilt from ~6 MB pages down to 17–75 KB each; do not inline images/blobs or add heavy assets.

## Pages

- `index.html` — 流程總覽 (overview): flights (collapsible) + 12-day summary table.
- `daily.html` — 每日行程 (day-by-day detail): the largest page; embedded Google My Maps iframe, 12 collapsible day sections (`d1`–`d12`) each with a 時間軸 (timeline), 住宿 (lodging), and 三餐 (meals) block, plus 備忘/打包/雨天備案 sections.
- `hotels.html` — 住宿 (hotels): 9 nights / 7 stays.
- `transport.html` — 交通 (transport): flights, rail/boat, rental car, apps.
- `norway-todo.html` — 待辦 (checklist): interactive, state persisted in `localStorage`.

## Architecture and conventions

**Every page is fully self-contained; shared chrome is duplicated, not templated.** The top nav, hero banner, footer, `<head>` (Noto Sans TC Google Font, tiny `<style>` block with `.hv1/.hv2/.hv3` hover classes), and design tokens are copy-pasted across all five files. Any change to the nav, footer, or shared styling must be applied to all five pages by hand. Each page marks itself active in the nav with `color:#25313d;font-weight:700` while other links use `color:#8a97a4` + class `hv1`.

**All styling is inline `style=""` attributes.** The only stylesheet content is the 4-line `<style>` block for hover states. Match this pattern when adding content — don't introduce CSS classes, external stylesheets, or frameworks.

**Shared design tokens** (used consistently across pages): dark ink `#25313d`, link blue `#3d6b8f`, muted gray `#8a97a4`, secondary text `#61707f`, dashed dividers `#dde5eb`, pill/chip background `#f3f6f8` with border `#e2e8ed`, warning text `#a07830` on `#fbf5e9`, hero gold accent `#e8d3a3`. Day numbers are color-coded by day type: purple `#6b5ca8` (flights), blue `#3d6b8f` (rail/NiN), teal `#2b8a80` (city day), green `#3e8a5f` (driving), amber `#a8742a` (Trolltunga hike). Content column is `max-width:680px`.

**Collapsible sections** use paired element ids and vanilla JS at the bottom of the page:
- `daily.html`: each day `dN` has body `bd-dN` and arrow `ar-dN`; the `KEYS` array in its script drives `tg()`/`toggleAll()` and hash deep-linking (`#d7` auto-expands day 7). Adding/removing a day requires updating the section ids, the quick-nav pills, and `KEYS`.
- `index.html`: the flights block uses `fBody`/`fArrow`/`fHint` with `tgF()`, and auto-opens on `#flights`.

**Checklist state** (`norway-todo.html`): each item needs three ids — `bx-<id>` (box), `mk-<id>` (checkmark), `lb-<id>` (label) — and its id added to the `ITEMS` array. State is stored in `localStorage` under key `norwayTodoV1`; bump the key if item semantics change incompatibly.

**Content conventions**: ✅ marks a confirmed/booked item; ⚠️/⚠ marks a caution note. Hotel and restaurant names link to Google Maps search URLs (`https://www.google.com/maps/search/?api=1&query=...`) with `target="_blank" rel="noopener"` and class `hv3`. Itinerary facts (flight numbers, ferry times, hotel nights) appear on multiple pages — when the plan changes, update every page that mentions the fact (typically `index.html`, `daily.html`, and `hotels.html` or `transport.html`).

Git commit messages in this repo are conventionally written in Traditional Chinese.
