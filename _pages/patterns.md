---
layout: page
title: App Patterns — A Field Guide
permalink: /patterns/
---

A living reference of different ways to build apps, grounded in real projects built without a traditional server stack.

Each pattern captures how it works, what constraints it operates under, its limitations, and how it could be upgraded.

---

## How to Read This Guide

These patterns were built under a specific set of constraints: no rented server, no managed database, no DevOps overhead. The goal was always to ship something real and useful with the simplest possible infrastructure. That turns out to be a surprisingly wide design space.

The patterns are organized into three tiers by **where data lives and who controls the backend**:

### Tier 1 — Browser Only

No external service. Everything runs in the browser. Data lives on the device (or doesn't persist at all). Zero setup beyond a GitHub repo and Pages. The ceiling is whatever `localStorage` and the browser's file system can do.

### Tier 2 — GitHub as the Backend

GitHub's API, JSON files committed to a repo, and optionally GitHub Actions become the entire backend stack. Reads are free and public (raw.githubusercontent.com). Writes are authenticated via a Personal Access Token. Git history is the audit log. The CI/CD pipeline (Actions) handles any server-side processing like image optimization. Hosting is Cloudflare Pages.

### Tier 3 — Google as the Backend

Google Apps Script provides a real server-side execution environment — something neither the browser nor GitHub can offer. It runs with your Google credentials, reads and writes Google Sheets, and exposes a public HTTPS endpoint. The Sheet is the database; the Script is the API. No credit card, no cloud account, no deployment pipeline.

---

### How the Tiers Compare

|                                  | Tier 1        | Tier 2                    | Tier 3                            |
| -------------------------------- | ------------- | ------------------------- | ---------------------------------- |
| **Where data lives**             | Browser only  | GitHub repo (JSON files)  | Google Sheet                       |
| **Server-side code**             | None          | GitHub Actions (optional) | Google Apps Script                 |
| **Auth**                         | None          | GitHub PAT                | Google account / org               |
| **Write from phone**             | Yes           | Yes (via GitHub API)      | Yes (via Apps Script POST)         |
| **Data survives browser clear**  | ❌             | ✅                         | ✅                                  |
| **Multi-device**                 | ❌             | ✅                         | ✅                                  |
| **Data is public**               | No            | If repo is public         | If Apps Script access is "Anyone"  |
| **Setup complexity**             | Minimal       | Moderate                  | Moderate                           |
| **Cost**                         | Free          | Free                      | Free                                |

---

## Global Constraints

These apply to **all patterns** in this guide unless noted otherwise:

| Constraint                  | Detail                                                                                            |
| ---------------------------- | --------------------------------------------------------------------------------------------------- |
| **Hosting**                  | GitHub Pages or Cloudflare Pages (static files only — no server-side code execution on the host)   |
| **No traditional backend**   | No Node/Python/etc. server, no managed database, no API keys hidden server-side                    |
| **No built-in auth**         | No login system unless bolted on via a third-party service                                          |
| **HTTPS only**               | Pages hosting serves over HTTPS — mixed-content HTTP calls will be blocked                          |
| **Secrets**                  | Any API key or token in a GitHub repo or HTML file is visible — treat all as public                 |

---

## Pattern Template

```
### Pattern N: [Name]

**What it is**
One sentence description.

**How it works**
Brief technical description — what runs where, what the user sees.

**Examples**
- [Project name](URL) — what it does

**Constraints (beyond global)**
Any additional constraints specific to this pattern.

**Limitations**
- Bullet list of real-world limits

**How it could be upgraded**
- Bullet list of improvements
```

---

## Tier 1 — Browser Only

---

### Pattern 1: Static Single-File App with Session-Only State

**What it is**
A self-contained HTML file with all logic, styles, and UI in one place. State lives in the browser for the current session only — intentionally ephemeral.

**How it works**
One `.html` file holds HTML structure, CSS styling, and JavaScript logic. The app runs entirely in the browser — no server calls. State is written to `localStorage` so it survives a page refresh within the same browser/device, but there is no persistence across devices or days. In this specific example, the "reset" workflow is the design: tasks are meant to be cleared each day.

**Examples**
- [135-todo.html](https://github.com/neely/apps/blob/main/135-todo.html) — A 1-3-5 daily task planner (1 big thing, 3 medium, 5 small). Built March 8, 2026.

**Constraints (beyond global)**
- Everything is in one file — no build step, no framework, no npm. Deploy by dropping the file in a repo with GitHub Pages enabled.
- `localStorage` is per-browser, per-device. Clearing browser data wipes it.
- No sharing — state is not accessible to anyone else or on another device.

**Limitations**
- Tasks do not persist across devices or browsers.
- No history — once you reset, it's gone.
- No user identity — anyone on the same browser/device shares the same state.
- Storage cap: `localStorage` is typically limited to ~5MB per origin.
- Not suitable for anything sensitive — data sits unencrypted in the browser.

**How it could be upgraded**
- Add a simple backend (e.g. Supabase, Firebase) to persist tasks across devices.
- Add auth (e.g. Clerk, Auth0, or GitHub OAuth) to tie state to a user identity.
- Add date-stamped history so completed days are archived, not lost.
- Use `IndexedDB` instead of `localStorage` for larger or more structured local data.
- Package as a PWA (Progressive Web App) for offline use and a home screen icon.

---

### Pattern 2: Static Single-File App with localStorage + Manual JSON Backup

**What it is**
Same single-file, no-backend foundation as Pattern 1, but adds an explicit escape hatch for the biggest localStorage risk: data loss. Users can export their data as a JSON file and re-import it to restore state.

**How it works**
State is read/written to `localStorage` on every change. Two buttons in the UI let the user:

- **Export** — serializes the current state to a JSON file and triggers a browser download (named with today's date, e.g. `queue-2026-03-19.json`)
- **Import** — opens a file picker, reads the selected JSON file, validates it, and overwrites localStorage with the restored data

No server is involved at any point. The JSON file is the user's portable "save file" — they own it and manage it themselves.

Also introduces a third-party JS dependency loaded from a CDN: [SortableJS](https://sortablejs.github.io/Sortable/) for drag-and-drop reordering.

**Examples**
- [media-tracker.html](https://github.com/neely/apps/blob/main/media-tracker.html) — "Queue": a media backlog tracker for books, movies, TV, and games with drag-to-reorder, mark-as-done, and JSON backup. Built ~March 19, 2026.

**Constraints (beyond global)**
- Still no server — the JSON file lives wherever the user saves it (Downloads folder, iCloud, etc.). They are responsible for not losing it.
- CDN dependency: SortableJS is loaded from `cdn.jsdelivr.net`. If that CDN is down, drag-to-reorder breaks (the rest of the app still works).
- Import/export is a manual, intentional act — it does not happen automatically.

**Limitations**
- No automatic backup — if the user forgets to export before clearing their browser, data is gone.
- No sync — the JSON file is a snapshot. If the user updates the app on one device and imports the old file on another, they'll overwrite their newer data.
- No version control on the JSON — there's no diff or merge; import is a full overwrite.
- Still single-user, single-device by default.
- CDN availability is a soft dependency.

**How it could be upgraded**
- Auto-export on every change (write JSON to a synced folder via the File System Access API — only works in Chrome/Edge).
- Replace manual import/export with cloud sync (e.g. save JSON to Google Drive or Dropbox via their APIs).
- Add a proper backend + database so state is always server-side and the export is just a bonus.
- Version the JSON exports (add a timestamp or version field) to make it easier to identify which backup to restore.
- Add merge/diff logic on import instead of full overwrite — so you don't accidentally lose newer items.

---

## Tier 2 — GitHub as the Backend

---

### Pattern 3: JAMstack — GitHub as Database + CDN + CI/CD

**What it is**
A multi-file static site where GitHub itself plays every backend role: the JSON data file is the database, the GitHub API is the write endpoint, GitHub Actions is the processing pipeline, and Cloudflare Pages is the CDN/host. The result is a fully phone-operable, publicly readable, write-protected app with real image processing — no server, no cloud account beyond GitHub and Cloudflare free tiers.

**How it works**
The app has two HTML pages and a clear separation of read vs. write:

- **`index.html` (public reader)** — fetches `hikes.json` directly from `raw.githubusercontent.com` (no auth required, public repo). Renders entries as cards with swipeable photo galleries, stats, notes, and trail maps. A floating `+` button links to `add.html`.
- **`add.html` (authenticated writer)** — handles creating and editing entries. On first use, a modal prompts for a GitHub Personal Access Token (PAT) with `repo` scope, which is stored in `localStorage`. All writes go through the GitHub Contents API (`api.github.com`) authenticated with that token. The PAT is never sent anywhere except directly to GitHub over HTTPS.
- **`hikes.json` (the database)** — a single JSON file committed to the repo. It is the entire data layer. Every hike is an entry in a top-level `hikes` array with a slug-based `id`, trail metadata, stats, notes, and paths to processed image assets.
- **The image pipeline (GitHub Actions)** — uploading photos doesn't write them to their final location directly. `add.html` commits raw images to a `staging/{slug}/` folder, then updates `hikes.json` with the expected final asset paths. A GitHub Action (`process-images.yml`) triggers on any push touching `staging/`, runs Python/Pillow to resize images to 2000px long edge, convert to WebP (quality 75, method 6), apply EXIF rotation, strip metadata, compute aspect ratios, updates `hikes.json` with the ratios, and commits everything to `assets/images/{year}/{slug}/`. The browser then polls `raw.githubusercontent.com` waiting for the processed files to appear before redirecting to the journal.
- **Hosting** — Cloudflare Pages connected to the GitHub repo. Every push auto-deploys. No build step — Cloudflare just serves the files. A custom domain is a CNAME record in Cloudflare DNS.

**File structure**

```
hiking-journal/
├── index.html                        # Public journal reader
├── add.html                          # Authenticated add/edit form
├── hikes.json                        # The entire database
├── assets/images/{year}/{slug}/      # Processed WebP photos + maps
├── staging/                          # Temporary upload area (cleared by Action)
└── .github/workflows/process-images.yml
```

**Examples**
- [neely/hiking-journal](https://github.com/neely/hiking-journal) — "Neely Trails": a family hiking journal with photos, stats, trail maps, and notes. Add/edit from any phone with the PAT saved. Built ~April 14, 2026.

**Constraints (beyond global)**
- Repo must be **public** for `index.html` to read `hikes.json` from `raw.githubusercontent.com` without auth. A private journal would require routing reads through the authenticated API too.
- The PAT has `repo` scope — full read/write to the repo. It's powerful; treat it like a password.
- The PAT is stored in `localStorage` on each device. Not secure against someone with physical access to the browser, but never exposed publicly.
- GitHub Actions is unlimited for public repos; private repos have a 2,000 minutes/month free tier. The image processing Action is lightweight — typically under 30 seconds per run.
- Cloudflare Pages CDN introduces a small propagation delay between a GitHub commit and the file being visible at the custom domain. The browser polling loop in `add.html` accounts for this.
- The concurrency block in the Action prevents race conditions when photos are uploaded in multiple bursts; a `git pull --rebase` at the start of the Action handles commit timing edge cases.

**Limitations**
- **Write latency** — saving a hike with photos takes 1–3 minutes: commit staging files one by one via API → commit `hikes.json` → wait for the Action (~30–60s) → poll for processed images.
- **Single writer** — no conflict resolution. If two devices save simultaneously, the second commit will fail or overwrite the first.
- **No real auth** — the PAT is all-or-nothing `repo` scope. No roles (viewer vs. editor), no per-user access.
- **Deletion is incomplete** — deleting a hike removes it from `hikes.json` but leaves image files in `assets/images/`. The repo grows even if entries are deleted.
- **GitHub as a database has limits** — the Contents API can't efficiently query or filter `hikes.json`. For hundreds of entries, parsing the whole file every page load becomes slow.
- **No offline support** — reads require a network call to `raw.githubusercontent.com`. No service worker or caching layer.

**How it could be upgraded**
- Add a `manifest.json` and service worker to make it a full PWA with offline reading.
- Split `hikes.json` into per-year files or a file-per-hike to reduce payload as the journal grows.
- Add drag-to-reorder on the photo upload list so hero shot selection is explicit.
- Route reads through the authenticated API (and make the repo private) for a truly private journal.
- Replace the PAT with GitHub OAuth (via a small Cloudflare Worker) to avoid storing a powerful token in `localStorage`.
- Add a Cloudflare Worker as a thin write proxy to hide the PAT entirely from the browser.

---

### Pattern 4: GitHub as Database — Multi-Audience, PAT-Gated Write, No Pipeline

**What it is**
A refinement of Pattern 3, stripped of the image processing pipeline and optimized for a two-audience app: a fully public read-only view for one group (kids) and a PAT-gated admin interface for another (parents). Key evolutions over Pattern 3: `sessionStorage`-only PAT handling, fine-grained PAT scoping, and optimistic UI updates that bypass CDN propagation delay.

**How it works**
Two HTML files, two JSON files, one GitHub repo:

- **`index.html` (kid dashboard)** — fetches `ledger.json` and `investments.json` directly from `raw.githubusercontent.com` (no auth, public repo). Computes balances on the fly by walking transactions sorted by date then sequence number — balances are never stored, always derived. Shows per-kid cards, transaction history with relative dates ("3 days ago"), date range filters, and live search. Designed to be bookmarked by kids.
- **`bank.html` (parent admin)** — on every new browser session, a fullscreen prompt requires a GitHub PAT before anything renders. The token is validated against the GitHub API before the app loads. Stored in `sessionStorage` only — clears when the tab closes, never persists to `localStorage`. Add, edit, and delete transactions via bottom sheets. Update investment balances. All writes use the GitHub Contents API (fetch current SHA → patch in-memory ledger → PUT back). The UI updates immediately from memory rather than re-fetching from the CDN, which sidesteps propagation delay entirely.
- **`ledger.json`** — a flat array of all transactions for all kids. Balances are computed at runtime, never stored. Git commit history is the audit log — every write creates a descriptive commit (`Add transaction for Adam`, `Delete transaction adam-1 for William`).
- **`investments.json`** — a separate flat array for investment account balances (SPY, Roth IRA, etc.) tracked alongside but excluded from spendable balance.
- **Hosting** — Cloudflare Pages + custom domain via CNAME at the domain registrar. No build step.

**Examples**
- [neely/kid-bank](https://github.com/neely/kid-bank) — "Neely Bank": a family banking ledger for tracking kids' allowances, spending, and investments. Two kids, two audiences, one JSON file. Built 2026.

**Constraints (beyond global)**
- Repo must be public for `index.html` to read without auth.
- Fine-grained PAT scoped to Contents read/write only — meaningfully tighter than the broad `repo`-scope PAT in Pattern 3.
- `sessionStorage` only for the PAT — clears when the tab closes. On a phone, Face ID or device passcode protects the saved keychain entry.
- No GitHub Action — writes go directly to the final JSON files, so there's no staging step or propagation delay to wait out on the write side.
- Cloudflare CDN propagation delay still exists for the public `index.html` view, but the admin `bank.html` updates its UI from memory immediately.

**Limitations**
- **Public data** — `ledger.json` and `investments.json` are readable by anyone who finds the raw GitHub URL. Acceptable for a family fun ledger; not for anything sensitive.
- **Single writer** — no conflict resolution if two sessions write simultaneously.
- **No real audit trail UI** — Git history is the audit log, but there's no in-app way to view or search it.
- **Data grows without pruning** — `ledger.json` accumulates every transaction forever.
- **Investment values are manual** — no market data integration.
- **Layout designed for 2–4 kids** — needs CSS changes to scroll gracefully beyond four.

**How it could be upgraded**
- Add a Cloudflare Worker as a write proxy to hide the PAT server-side entirely.
- Replace the PAT with GitHub OAuth (via a Worker or small auth service) for a proper login flow.
- Pull live investment prices from a public market data API and auto-update `investments.json` via GitHub Actions.
- Archive old transactions to a `ledger-archive.json` annually, keeping the active file small.
- Add a `manifest.json` and service worker to cache `index.html` and the last-fetched ledger for offline reading.
- Make the repo private and route all reads through the authenticated API.

---

## Tier 3 — Google as the Backend

---

### Pattern 5: Google Apps Script — Self-Contained App with Sheet as Database

**What it is**
A web app that lives entirely inside Google's ecosystem. The Google Sheet is the database, Google Apps Script provides a genuine server-side backend, and the deployed URL is a Google-hosted web app. GitHub is only involved as a place to document or blog about it — the app itself runs completely outside of it.

**How it works**
Everything lives inside a single Google Sheet:

- **The Sheet** — acts as the database. Headers in row 1 define the schema; each subsequent row is a record. Data can be entered directly into the sheet, via a Google Form, or via an AI assistant prompt (see below).
- **`Code.gs` (the backend)** — a Google Apps Script file attached to the Sheet. `doGet()` serves the HTML page when the web app URL is hit. `getData()` reads the entire sheet and returns it as a 2D array to the frontend. This runs server-side on Google's infrastructure — it has real access to the Sheet, the user's Drive, and other Google services.
- **`Index.html` (the frontend)** — an HTML file also inside Apps Script. The browser loads it, then calls `google.script.run.withSuccessHandler(fn).getData()` — a special Apps Script bridge that calls the server-side function and passes the result back to the client-side JS. The frontend renders the data as a sortable, filterable dashboard table with expandable detail rows.
- **The AI data entry workflow** — a Gemini prompt acts as a "data coordinator": it reads the live sheet via Google Drive tool access, deduplicates against existing rows, assigns IDs and priorities from the provided text/emails, and outputs a Markdown table ready to paste into the sheet. No code required for data entry.
- **Deployment** — in the Apps Script editor, Deploy → Web App. Set "Execute as: Me" and "Who has access: Anyone within your organization." This gives everyone at your org a dashboard link without requiring them to have sheet access or a GitHub account.

**Examples**
- [GoogleSheetSite](https://neely.github.io/GoogleSheetSite/) — a clearance request tracker for a lab/org. Priority-coded rows, lab badges, sortable columns, expandable detail panels, search + filter controls. Built March 31, 2026. (GitHub hosts the blog post; the actual app lives at a Google Web App URL.)

**Constraints (beyond global)**
- **Not on GitHub Pages** — the app is deployed via Google, not GitHub. GitHub is used here only as a blog/documentation host.
- **Google account required** — anyone accessing the dashboard must be signed into a Google account belonging to the organization (or set access to "Anyone" for fully public).
- **"Execute as: Me"** — the script runs with your Google credentials. If your account is suspended or the Sheet is moved, the app breaks for everyone.
- **Apps Script quotas** — daily limits on execution time, URL fetch calls, etc. Essentially never hit for small org dashboards.
- **No version control on the Script** — Apps Script has its own versioning (you must create a new version to update the deployed app), separate from Git.

**Limitations**
- **Org-only by default** — sharing outside your Google Workspace org requires changing the deployment access, which may conflict with org policies.
- **Sheet = database, with all that implies** — no foreign keys, no joins, no transactions. Concurrent edits are handled by Google Sheets but there's no row-level locking.
- **Redeploy to update UI** — changing `Index.html` requires creating a new deployment version. The URL stays the same but the step is manual.
- **No real auth/roles** — access is binary: org member (can view) or you (can edit). No per-user permissions, no audit log of who changed what row.
- **Data read is a full fetch** — `getData()` returns the entire sheet every page load. Fast for hundreds of rows; sluggish for thousands.
- **AI data entry is manual-assisted, not automated** — the Gemini workflow requires a human to paste the output into the sheet.

**How it could be upgraded**
- Add a Google Form as the official data entry path — form responses write directly to the sheet automatically.
- Use Apps Script triggers (e.g. `onFormSubmit`) to auto-assign IDs, set priority based on deadline, or send email notifications.
- Add row-level edit controls inside the dashboard — the web app UI could call an `updateRow()` Apps Script function to write back.
- Replace the full-sheet fetch with a filtered `getData(filter)` call that only returns matching rows.
- Migrate to a more robust backend if the data grows — export the sheet to a real database and replace `getData()` with an API call.

---

### Pattern 6: Google Apps Script — Shared Backend, Multiple Apps, Separate Dashboard

**What it is**
An evolution of Pattern 5 where the Apps Script is no longer a single self-contained app but a **shared API endpoint** that multiple independent front-end apps all write to, and a separate read-only dashboard reads from. The Apps Script becomes a true micro-backend: a persistent, serverless data layer that any static HTML file can talk to over HTTPS.

**How it works**
Four files with distinct roles:

- **`Code.gs` (the shared backend)** — one Google Apps Script deployed as a web app. `doPost()` accepts JSON from any workout app and routes it to either a `Sessions` sheet (one row per completed workout) or a `Rounds` sheet (one row per set/round). `doGet()` reads the entire Sessions sheet and returns it as JSON for the dashboard. A single `/exec` URL serves both reads and writes. The script handles schema creation (`ensureSheets()`) on first run — no manual spreadsheet setup needed.
- **Workout apps (e.g. `iron-tide.html`)** — fully self-contained, phone-optimized single-file apps. Each implements a specific training program (Iron Tide is an 8-week kettlebell program with Day A and Day B sessions). The app guides you through the workout in real time with EMOM arc timers, cadence clocks, round counters, and phase navigation. On session end, it fires `fetch()` POST calls to the Apps Script URL using `mode: 'no-cors'` — fire-and-forget, no response is read. If the POST fails, the workout app doesn't know or care; the UX is unaffected.
- **`workout-dashboard.html` (the reader)** — a completely separate static file. On load, it fetches the Apps Script `/exec` URL via `doGet()`, receives the full Sessions array as JSON, and renders a polished analytics dashboard: weekly kg tonnage bar chart, GitHub-style workout consistency heatmap (8W/3M/6M/1Y), biggest days leaderboard, and per-day detail cards. Zero write capability — pure read.
- **The Google Sheet** — invisible to the user but central. Two tabs: `Sessions` and `Rounds`. The sheet is the persistent store; Apps Script is just the access layer.

**The architecture in one sentence:** workout apps → `fetch POST /exec` → Apps Script `doPost()` → Google Sheet rows; dashboard → `fetch GET /exec` → Apps Script `doGet()` → JSON → rendered analytics.

**Examples**
- `iron-tide.html` — Iron Tide: an 8-week double kettlebell program (ABC complex + carries on Day A; press ladder + snatches + push/pull on Day B). EMOM arc timer, phase navigation, real-time round logging, session summary. PWA-ready with apple-touch-icon.
- `workout-dashboard.html` — a read-only analytics dashboard aggregating sessions from all programs into a single view with tonnage charts, heatmap, and session history.

**Constraints (beyond global)**
- The Apps Script `/exec` URL is **public in the HTML source** of every workout app. Anyone who finds it can POST fake sessions or GET all your data. Acceptable for a personal fitness log; not for sensitive data.
- `mode: 'no-cors'` is required for POST calls from a browser to Apps Script (Apps Script doesn't return CORS headers on POST). This means the workout app **cannot read the response** — no confirmation, no error handling. Fire and forget.
- The dashboard's `doGet()` read **can** read the response (GET requests do return CORS headers) — so the dashboard knows if the fetch failed and shows a helpful error state.
- Apps Script has a 30-second execution timeout and daily quotas (essentially never hit for personal use).
- Wake Lock API (`navigator.wakeLock`) keeps the phone screen on during a workout — supported in Chrome/Edge, not Firefox, inconsistent in older Safari.

**Limitations**
- **No write confirmation** — a network failure during POST silently loses that workout's data. No retry, no queue, no offline fallback.
- **All data is readable** — the `/exec` URL exposes your entire workout history as a public JSON endpoint. Security through obscurity only.
- **No server-side filtering** — `doGet()` returns all sessions every time. The dashboard filters client-side; payload grows unbounded over years.
- **Schema is fixed** — adding a new field (e.g. heart rate) requires updating `Code.gs`, redeploying, and updating every workout app.
- **Shared URL across all apps** — if you redeploy to a new URL, every workout app needs updating.
- **No deduplication** — tapping "End" twice creates a duplicate row. No detection in the dashboard.

**How it could be upgraded**
- Write a fallback to `localStorage` when offline and flush to the sheet on next load (manual retry queue).
- Move the Apps Script URL to a config object or URL param so all apps share one place to update it.
- Add a `?program=IronTide` filter param to `doGet()` so the dashboard requests only what it needs.
- Add a lightweight auth check in `doPost()` — e.g. a shared secret in the POST body — to reject random POSTs.
- Replace `no-cors` fire-and-forget with a Cloudflare Worker proxy that accepts the POST, confirms receipt, and forwards to Apps Script — giving the workout app write confirmation.
- Add `IndexedDB` as a local cache so sessions are saved locally first and synced when connectivity is confirmed.

---

## Where Hyde Fits

[Hyde](https://github.com/neely/hyde) — the Jekyll post composer covered in [this post](/Building-a-Mobile-First-Post-Composer-for-Jekyll/) — is a close cousin of **Pattern 4**: GitHub as database, fine-grained PAT-gated write, no processing pipeline. The difference is what's being written — Markdown posts and front matter instead of JSON ledger rows — but the underlying mechanics (Contents API, SHA-based updates, no server) are the same shape.

---

## Changelog

| Date       | What was added                                                                                                     |
| ---------- | -------------------------------------------------------------------------------------------------------------------- |
| 2026-05-18 | Document created                                                                                                     |
| 2026-05-18 | Pattern 1: Static Single-File App — Session-Only State (135-todo.html)                                               |
| 2026-05-18 | Pattern 2: Static Single-File App — localStorage + Manual JSON Backup (media-tracker.html)                           |
| 2026-05-18 | Pattern 3: JAMstack — GitHub as Database + CDN + CI/CD (hiking-journal)                                              |
| 2026-05-18 | Pattern 4: GitHub as Database — Multi-Audience, PAT-Gated Write, No Pipeline (kid-bank)                              |
| 2026-05-18 | Pattern 5: Google Apps Script — Self-Contained App with Sheet as Database (GoogleSheetSite)                          |
| 2026-05-18 | Pattern 6: Google Apps Script — Shared Backend, Multiple Apps, Separate Dashboard (iron-tide + workout-dashboard)    |
| 2026-05-18 | Reorganized into tiers with intro and comparison table                                                               |
| 2026-06-25 | Added Hyde cross-reference                                                                                           |
