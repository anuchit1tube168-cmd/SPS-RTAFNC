# CLAUDE.md

This file guides Claude Code (and other AI assistants) when working in this repository.

## Project overview

**SPS-RTAFNC** — *ระบบจัดซื้อจัดจ้าง วิธีเฉพาะเจาะจง* (Special Procurement System) is a
single-page prototype web app for managing Thai government procurement by the
"specific method" (วิธีเฉพาะเจาะจง) for วิทยาลัยพยาบาลทหารอากาศ (Royal Thai Air Force
Nursing College).

It is a **static frontend prototype** with no build step and no backend. All data lives
in the browser via `localStorage`, so it can be hosted directly on GitHub Pages and is
meant for validating the workflow/UX before wiring a real backend (Google Sheets,
Firebase, Supabase, or a custom API).

- **Live site:** https://anuchit1tube168-cmd.github.io/SPS-RTAFNC/
- **UI language:** Thai. Keep all user-facing strings in Thai.

## Architecture

The entire application is **`index.html`** — one file containing HTML, CSS, and vanilla
JavaScript. There is no framework, bundler, package manager, or transpiler.

- **`index.html`** — the whole app (styles in one `<style>` block, logic in one `<script>`).
- **`manifest.webmanifest`** — PWA manifest (installable, standalone display).
- **`page-test.html`** — a standalone "Pages is live" check page.
- **`.nojekyll`** — tells GitHub Pages to skip Jekyll processing.
- **`.github/workflows/pages.yml`** — deploys the repo root to GitHub Pages on push to `main`.
- **`README.md` / `README-SPS.md`** — project docs (Thai).

### How the app works

State is a single object `S`, persisted to `localStorage` under the key `spsRTAFNC` via
`save()`. The app is rendered by string-templating `innerHTML`:

- `render()` — the router. Reads `S.user` / `S.view` and calls the matching view function.
- `shell(content)` — draws the sidebar + top bar chrome, then injects the view's `content`.
- View functions: `loginPage`, `dashboard`, `requests`, `create`, `detail`, `vendors`,
  `reports`, `logs`, `settings`.
- Navigation: `go(view)` sets `S.view`, saves, and re-renders (a full re-render, which is
  why the mobile sidebar auto-closes on navigation).
- Helpers: `baht()` (currency), `thaiDate()` (Thai date), `toast()` (notifications),
  `filtered()` (search/type/status filtering).

### Domain model

Each document (`S.docs[]`) has: `number, date, subject, type` (`buy`/`hire`), `group`,
`amount, status, vendor, owner, reason, budget, days, items[]`.

- **Roles** (`roles`): `admin`, `director`, `head`, `officer`. Demo password is `123456`.
- **Status workflow** (`labels` + `next`): `draft → review → director → consider → order
  → inspected → complete` (plus `cancel`). `advance()` moves a doc to the next status.
- **Printing:** `printDoc()` and `printReport()` open a new window with print-ready
  government-style HTML; `exportCSV()` downloads a UTF-8 BOM CSV.

## Working in this repo

- **No build/install/test commands** — there is no toolchain. Edit `index.html` and open
  it in a browser (or push to see it on GitHub Pages).
- **To preview locally:** open `index.html` directly, or run a static server such as
  `python3 -m http.server` from the repo root.
- **Deployment is automatic:** pushing to `main` triggers `.github/workflows/pages.yml`.
  This repo's feature work happens on a `claude/*` branch and is merged to `main`.
- **Reset demo data:** Settings → "รีเซ็ตข้อมูลตัวอย่าง", or clear the `spsRTAFNC`
  localStorage key.

## Conventions

- **Keep it dependency-free.** No npm packages, no external JS/CSS beyond the Google Fonts
  link already present. Everything must work as a static file opened from disk.
- **One file.** Prefer keeping app logic in `index.html`. Only split files when there's a
  clear reason.
- **Thai UI copy.** All labels, messages, and document text are Thai. Match the existing
  formal/government tone.
- **Responsive first.** The app must work on phone, tablet, and desktop. Preserve the
  mobile sidebar (`toggleMenu`/`closeMenu` + backdrop), the `viewport-fit=cover` safe-area
  handling, and the breakpoints at 1120 / 900 / 760 / 440px. Test narrow widths after any
  layout change.
- **Accessibility:** keep `:focus-visible` outlines, `aria-label`s on icon-only buttons,
  16px form inputs (prevents iOS zoom-on-focus), and the reduced-motion media query.
- **State compatibility:** existing users have data in `localStorage`. When changing the
  shape of `S`, keep it backward-compatible or handle migration gracefully.

## Git / PR notes

- Development branch for the current effort: `claude/responsive-design-clause-docs-lalxxe`.
- Do not push to `main` directly without explicit permission; open PRs against `main`.
- Only create a pull request when the user explicitly asks for one.
