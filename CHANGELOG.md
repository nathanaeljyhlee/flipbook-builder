# 📜 Changelog

All notable changes to **flipbook-builder**. Newest first.

The project follows loose semantic versioning. Dates are absolute (not "X days ago") so the doc ages gracefully.

---

## v1.4 — 2026-05-11
**Customizable book aspect ratio + credit URL overflow fix**

Added
- 📐 **Shape picker** in the meta panel — three presets that let authors pick the book proportions:
  - 📕 **Tall** (0.72) — default, chapter-book / novel proportions
  - 📒 **Board** (1.2) — board-book proportions, ~60% shorter than Tall
  - 📰 **Wide** (1.5) — landscape picture-book spread
- New state field `bookAspect` (default `0.72`, backwards-compatible loadState migration)
- Aspect is embedded in the exported standalone HTML so shared books keep their shape

Fixed
- 🐛 **Credit URL overflow** — long URLs in `imageCredit` (e.g., Flickr links) weren't wrapping inside the credit's `max-width` box, pushing the last line into the page-number area. Added `overflow-wrap: anywhere` + `word-break: break-word` to `.credit` and `.credit a`. Mirrored into the exported viewer CSS.

Tests
- 10 new tests on top of the existing 29 — aspect picker behavior + credit overlap measurement. All passing.

Commits: `ec2f680`

---

## v1.3 — 2026-05-11
**Image attribution + live "last saved" indicator**

Added
- 📷 **Per-page image attribution** — two new optional fields per page (`imageCredit` text + `imageCreditUrl` link). Renders as a tiny italic line at the bottom of every layout (image-top, fullbleed, big-quote, side, cover, back). Clickable when URL is set. XSS-safe.
- 🕒 **Live "saved X min ago" indicator** in the storage bar. Refreshes every 30 seconds without re-saving. Falls back to local-time clock format for stale (>24h) sessions.
- Mirrored credit rendering into the standalone HTML export and the Print-to-PDF stylesheet so credits travel everywhere.

Tests
- 5 new attribution tests + 3 new last-saved tests on top of the regression suite. 29/29 passing.

Commits: `45410b5`, `cecdbc9`

---

## v1.2 — 2026-05-11
**File System Access API — true file persistence (hotfix)**

Added
- 💾 **Save to file** + 📂 **Open file** buttons (Chrome/Edge). Authors pick a real file on disk once; every subsequent edit auto-writes to it in the background. Survives browser-clear-data, profile resets, even moving to another machine via Drive/OneDrive sync.
- Storage bar shows 🔵 `<filename>` when linked
- Graceful fallback on Firefox/Safari — buttons dim with explanatory tooltip; localStorage auto-save remains

Why
- Previous auto-save was localStorage-only — vulnerable to clearing site data, hitting the ~5MB cap, or switching devices

Commits: `c4c8ace`

---

## v1.1 — 2026-05-11
**10x polish pass + recursive bug hunt**

Added (Tier 1 — visual & functional)
- 🎨 **Visual identity refresh** — Fredoka + Nunito typography, paper-texture page background via inline SVG noise, soft drop shadow under the book, peel-up flip hint
- 🎨 **4 cover themes** — Sunshine 🌞 / Ocean 🌊 / Forest 🌲 / Galaxy 🌌 via swatches in the editor
- 🗜️ **Automatic image compression** on upload — canvas resize to 1200px + JPEG re-encode @0.85 quality. Verified 6189KB → 69KB on a real phone photo. Eliminates the localStorage 5MB cliff.
- 📐 **Four per-page layouts** — image-top, full-bleed image with caption, big-quote text-only, image-left + text-right
- ↕️ **Drag-to-reorder pages** — native HTML5 DnD on content pages; cover/back pinned

Added (Tier 2)
- 🔔 Toast notifications stack (bottom-right, 3s auto-dismiss)
- ⏪ Undo / Redo with 20-state ring buffer (Ctrl+Z, Ctrl+Y, Ctrl+Shift+Z)
- 🧬 Duplicate page button
- 🖨️ Print stylesheet — Ctrl+P → save as PDF, editor chrome hidden
- 📖 Empty-state polish

Fixed
- 🐛 `StPageFlip.destroy()` detaches the container from DOM — subsequent `renderPreview()` crashed with `Cannot set properties of null`. Fixed by recreating the `#flipbook` div inside `#flipbook-wrap` on every render.
- 🐛 StPageFlip injects inline `display: block` on every page, overriding flex layouts. Fixed with `display: flex !important` on `.pf-page.cover`, `.pf-page.layout-side`, `.pf-page.layout-quote`.

Tests
- 23-test Playwright sweep — boot, persistence, edit propagation, multiline rendering, XSS escape, add/delete/drag/duplicate pages, image compression, bad-file rejection, layout/theme switching, JSON export/import, undo/redo, layout regression. 23/23 green.

Commits: `14f0f3a`

---

## v1.0 — 2026-05-11
**Initial scaffold**

The original split-pane editor with StPageFlip-driven live flipbook preview, localStorage auto-save, JSON export/import, Present mode (fullscreen), keyboard navigation. Single self-contained HTML file, no build step, no installs.

Stack
- Pure HTML / CSS / vanilla JS
- [StPageFlip](https://github.com/Nodlik/StPageFlip) (MIT) for the page-turn animation
- Google Fonts (Fredoka + Nunito)
- LocalStorage for auto-save

---

## 🛣️ Roadmap — Future ideas (unranked, not yet committed)

Things noted during the build that didn't make v1 but are worth revisiting:

### 🎨 Authoring polish
- **Noun Project icon combinations** — let authors pick from Noun Project icon library and compose multiple icons together as visual elements on a page. Especially useful for educational/conceptual children's books where the right "icon pair" is more flexible than a stock photo.
- License-picker dropdown for attributions (CC BY, BY-SA, BY-NC, public domain, etc.) — autofill correct attribution template
- Markdown `**bold**` / `*italic*` parsing inside body text
- Cover image upload + simple in-app crop
- Auto-extract attribution from image EXIF when present

### 📊 Workflow
- Onboarding coach-mark on first load (`localStorage` flag)
- Word count + estimated reading time per page card
- Bulk-attribution applied across pages (e.g., "all images from Unsplash")
- IndexedDB-persisted file handles so reopening the app auto-resumes the linked file (currently requires re-picking each session)

### 📱 Reach
- Mobile editing UI (current build assumes desktop; mobile is read-only)
- Audio narration (per-page MP3 + autoplay during page flip)
- Real-time co-editing (would need a server — out of scope for the single-file ethos)

### 🛠️ Dev
- Auto-fetch the StPageFlip browser bundle at export time and inline it, so exported books work fully offline on first open (currently the export references the CDN — fine for online viewers but not airgapped)
- Migrate from inline test suite to a `tests.spec.js` file runnable via `npx playwright test`

---

## Authoring conventions for future entries

When adding a new version, follow this shape:
```
## vX.Y — YYYY-MM-DD
**One-line headline**

Added / Fixed / Changed (use whichever apply)
- emoji + concise bullet

Tests
- short note on what was tested

Commits: short SHAs
```
