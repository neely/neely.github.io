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

## Available Templates

Three of the patterns below have (or will have) a public, ready-to-fork template — no personal data, just the app code:

| Template | Repo | Pattern | Status |
| -------- | ---- | ------- | ------ |
| Solo Agent Context Kit | [agent-context-project-template](https://github.com/neely/agent-context-project-template) | Development Workflow (below), not tied to one tier | Live |
| Hiking Journal | [hiking-journal-template](https://github.com/neely/hiking-journal-template) | Pattern 3 | Live — ships with a demo entry and screenshots |
| Kid-bank | — | Pattern 4 | Planned, not started |

---

## A Note on How These Get Built

The patterns above are snapshots of finished architectures. What they don't show is how each one actually gets built. That's a separate axis from *where data lives* — it's covered on its own in [Development Workflow](#development-workflow) at the end of this guide.

---

## Global Constraints

These apply to **Tiers 1–2's static-only patterns** (Patterns 1–6). Patterns 7 and 8 introduce a Cloudflare Worker and are the exception — noted in the right column:

| Constraint                  | Detail                                                                                            |
| ---------------------------- | --------------------------------------------------------------------------------------------------- |
| **Hosting**                  | GitHub Pages or Cloudflare Pages (static files only — no server-side code execution on the host). **Exception: Patterns 7–8** add a Cloudflare Worker alongside Pages — real server-side compute, just on a separate piece of infrastructure from the static host. |
| **No traditional backend**   | No Node/Python/etc. server, no managed database, no API keys hidden server-side. **Exception: Patterns 7–8** — the Worker *is* a minimal backend, purpose-built to hold exactly one thing server-side (a secret) rather than running general application logic. |
| **No built-in auth**         | No login system unless bolted on via a third-party service (Cloudflare Access, used throughout this guide, counts as bolted-on, not built-in)                                          |
| **HTTPS only**               | Pages hosting serves over HTTPS — mixed-content HTTP calls will be blocked                          |
| **Secrets**                  | Any API key or token in a GitHub repo or HTML file is visible — treat all as public. **Exception: Patterns 7–8** — a secret set as a Cloudflare Worker environment variable is genuinely private; it lives in Cloudflare's infrastructure, never in the repo or the browser. This is the whole point of those two patterns. |

---

## When to Graduate an App to Its Own Repo

Several patterns above (especially Tier 1) start life bundled — one repo, one Cloudflare Pages project, several small tools or pages sharing infrastructure. [kb-apps](https://github.com/neely/kb-apps) is the clearest example: nine kettlebell workout apps under one repo, one domain, one Access gate. Bundling is the right starting point — less setup, one place to look, no decision to make yet about whether a given tool deserves its own everything.

Splitting an app out into its own repo isn't just organizational tidiness once it happens — it unlocks things that are structurally unavailable while bundled. Worth checking against this list before deciding whether something's ready to graduate:

**Security**
- A dedicated Cloudflare Access policy, instead of one policy covering everything in the bundle whether it needs that level of protection or not.
- A PAT or Worker secret scoped to exactly this repo — bundled, any write-access token touches every sibling app too.
- A security posture that's a single clean fact ("private, gated") instead of "well, most of it is fine but one tool in here handles something sensitive."

**Infrastructure**
- Its own Cloudflare Worker — dedicated backend logic, own secrets, own rate limits (see [Pattern 7](#pattern-7-cloudflare-worker-as-full-backend-proxy--scheduled-action-pipeline) and [hiking-journal-proxy](https://github.com/neely/hiking-journal-proxy)).
- Its own GitHub Action — no need for path-based `on: push: paths:` triggers to avoid firing on unrelated sibling changes; every push in the repo is relevant by default.
- Independent deploy cadence — Cloudflare Pages ties 1:1 to a repo, so a bundled typo risks redeploying (and briefly breaking) everything in the bundle, not just the one app being changed.
- Preview deploys that mean something — a branch preview shows just this app's changes, not a diff across an unrelated bundle.

**Organizational**
- A README that's actually about one thing — see the [hiking-journal](https://github.com/neely/hiking-journal) rewrite, which replaced a doc trying to describe a moving multi-piece system with one that describes exactly what's there now.
- Issues and repo metadata (topics, description, `is_template`) that mean something at the app level instead of the bundle level.

**Collaboration & sharing**
- Forkability — this is literally how [hiking-journal-template](https://github.com/neely/hiking-journal-template) happened. A bundled app can't be cleanly forked without dragging in unrelated siblings.
- Room to add a collaborator on exactly one app without exposing anything else in the bundle.

**Once you've decided to graduate**
A newly split repo starts from zero — no history, no scaffolding. That's the right moment to set it up with the [Solo Agent Context Kit](https://neely.github.io/agent-context-kit/) (AGENTS.md/PLAN.md/NOTES.md/JOURNAL.md — see [Development Workflow](#development-workflow) below) from the first session, rather than bolting it on after the fact once there's already history to reconcile. deckhand and radio both started this way from session one; retrofitting it onto an app with existing history is more friction than it's worth.

**Signs it's not worth it yet**
- The app has no backend logic of its own and shares the exact same Access policy as its siblings — splitting adds repo-management overhead with no new capability unlocked.
- Nobody outside this bundle would ever want to fork or reuse just this one piece.

If none of the "possibilities" above actually apply yet, staying bundled is the simpler and correct choice — this isn't a call to fragment every small tool into its own repo preemptively, only to split when one of these becomes a real, current need.

---

## What Splitting CSS/JS Out of Inline HTML Unlocks

Most apps in this guide start as genuinely single-file HTML — inline `<style>` and `<script>`, everything in one place, easy to reason about for a small app. [hiking-journal](https://github.com/neely/hiking-journal) split its CSS/JS into external files in July 2026 once the reader/writer pages had enough duplicated design tokens and helper functions to be worth sharing (see the [How It Works](https://github.com/neely/hiking-journal#how-it-works) section there for the actual before/after). That split doesn't add capability by itself, but it removes friction that was making some things annoying enough to skip. Worth revisiting once an app has crossed that line:

**Near-zero-effort once split**
- **View Transitions** — `@view-transition { navigation: auto; }` in the shared stylesheet gets a native cross-fade between pages instead of a hard reload. The best "feels like an app" upgrade for the least code, and awkward to reason about correctly when styles were duplicated across multiple inline blocks.
- **Dark mode** — a `prefers-color-scheme` media query in the shared stylesheet, remapping existing design tokens. One place to update instead of every page's inline block staying in sync manually.
- **Print stylesheet** — a `@media print` block hiding chrome (buttons, nav) for anything meant to be printed or saved as PDF.
- **Shared loading/skeleton states** — once there's a shared stylesheet, a skeleton shimmer while data fetches is a small, one-place addition that benefits every page using it.

**Real new capabilities, still fully serverless**
- **A service worker (`sw.js`)** — needs addressable JS to register from. Unlocks: instant offline app-shell loading, cached assets, a real installable PWA rather than an imitation bookmark icon.
- **`manifest.json`** — pairs with the service worker to make "Add to Home Screen" produce an actual installed PWA (standalone window, proper icon behavior) instead of a Safari bookmark shortcut.
- **Web Share API** (`navigator.share()`) — native share-sheet integration from a card or entry, cleaner as addressable JS than buried in inline `onclick` handlers.
- **IndexedDB as an offline write queue** — for apps where writes might happen with no signal (e.g. logging something mid-trail), a small dedicated file can queue a write locally and flush it once back online. This is the one item here that's a genuine feature rather than polish — worth its own design pass before building, since it touches save/error-handling flow directly.
- **`robots.txt`** — trivial, but worth setting per-repo once files are addressable rather than baked into a single blob: `Disallow: /` for anything gated by Access (belt-and-suspenders past the actual gate), left open for anything meant to be public (like a template repo).

**Repo inventory — split vs. still inline (as of 2026-07-30):**

| Repo | Status |
|---|---|
| hiking-journal | Split — `style.css`, `year-palette.js`, `index.css`/`index.js`, `add.css`/`add.js` |
| deckhand | Split — `style.css`, `app.js` |
| recipes | Split — `style.css`, `recipe-engine.js`, `recipes.js` |
| radio | Inline — single `index.html` |
| kid-bank | Inline — `index.html`, `bank.html` |
| strikemap | Inline — `index.html`, `desktop.html` |
| kb-apps | Inline by design — `kb-lib.js` is shared, but it's a template library for building new tools in the bundle, not duplication debt to clean up. Leave as-is. |
| hiking-journal-template | Inline, deliberately — a fresh fork doesn't yet have the duplication that would justify splitting |

Not every repo in this list needs to change — it's here to make the option visible, not to imply a backlog.

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
A multi-file static site where GitHub itself plays every backend role: the JSON data file is the database, the GitHub API is the write endpoint, GitHub Actions is the processing pipeline, and Cloudflare Pages is the CDN/host. The result is a fully phone-operable app with real image processing — no server, no cloud account beyond GitHub and Cloudflare free tiers. Works equally well with a public or a private repo.

**How it works**
The app has two HTML pages and a clear separation of read vs. write:

- **`index.html` (reader)** — fetches `hikes.json` (and every image path) relative to its own domain — same-origin, served by whatever's hosting the page. No auth needed, and no dependency on the repo being public: Cloudflare Pages serves these files whether the source repo is public or private. Renders entries as cards with swipeable photo galleries, stats, notes, and trail maps. A floating `+` button links to `add.html`.
- **`add.html` (authenticated writer)** — handles creating and editing entries. The base version of this pattern stores a GitHub Personal Access Token (PAT) in `localStorage`, entered via a modal on first use, and writes go straight from the browser to `api.github.com`. The live instance ([neely/hiking-journal](https://github.com/neely/hiking-journal)) has since evolved past that: writes now go to `/api/contents/*` on the same domain, proxied by a small Cloudflare Worker ([neely/hiking-journal-proxy](https://github.com/neely/hiking-journal-proxy)) that holds the PAT as a server-side secret — see Pattern 7 for the general shape of that proxy. `add.html` itself never handles a credential at all anymore; Cloudflare Access (already gating the domain) is the only thing standing between a family member and the write path. The template repo still ships the simpler browser-PAT version, since it doesn't assume Access is set up.
- **`hikes.json` (the database)** — a single JSON file committed to the repo. It is the entire data layer. Every hike is an entry in a top-level `hikes` array with a slug-based `id`, trail metadata, stats, notes, and paths to processed image assets.
- **The image pipeline (GitHub Actions)** — uploading photos doesn't write them to their final location directly. `add.html` commits raw images to a `staging/{slug}/` folder, then updates `hikes.json` with the expected final asset paths. A GitHub Action (`process-images.yml`) triggers on any push touching `staging/`, runs Python/Pillow to resize images to 2000px long edge, convert to WebP (quality 75, method 6), apply EXIF rotation, strip metadata, compute aspect ratios, updates `hikes.json` with the ratios, and commits everything to `assets/images/{year}/{slug}/`. The browser then polls the same domain (not GitHub directly) waiting for the processed files to appear before redirecting to the journal.
- **Hosting** — Cloudflare Pages connected to the GitHub repo (works with a private repo via Cloudflare's own GitHub App install — unrelated to the public `raw.githubusercontent.com` CDN). Every push auto-deploys. No build step. A custom domain is a CNAME record in Cloudflare DNS.
- **Optional: going private** — flip the repo private and put a Cloudflare Access application (email + one-time PIN) in front of the custom domain *and* the auto-provisioned `.pages.dev` fallback domain (easy to forget the second one — it's an open back door if left ungated). No code changes required; the app never depended on the repo being public in the first place once reads are same-origin. The only tradeoff is a short Pages-redeploy delay after saving a new hike (same delay exists in the public version too — it's inherent to same-origin reads, not to Access).

**File structure**

```
hiking-journal/
├── index.html                        # Journal reader
├── index.css / index.js              # Reader-specific styles/script
├── add.html                          # Authenticated add/edit form
├── add.css / add.js                  # Writer-specific styles/script
├── style.css                         # Shared design tokens + reset
├── year-palette.js                   # Shared year-color palette + lookup fn
├── hikes.json                        # The entire database
├── trail_icon.png                    # Hosted favicon / iOS home screen icon
├── assets/images/{year}/{slug}/      # Processed WebP photos + maps
├── staging/                          # Temporary upload area (cleared by Action)
└── .github/workflows/process-images.yml
```

The reader/writer pages started as single self-contained HTML files (inline `<style>`/`<script>`) and were later split into external CSS/JS once the two pages accumulated enough duplicated design tokens and helper functions to be worth sharing — this is optional per-project; the template still ships inline for simplicity, since a fresh fork doesn't yet have the duplication that justifies splitting.

**Examples**
- [neely/hiking-journal](https://github.com/neely/hiking-journal) — "Neely Trails": a family hiking journal with photos, stats, trail maps, and notes. Private repo, gated with Cloudflare Access. Built ~April 14, 2026. Writes moved to a Worker-proxied PAT (below) and CSS/JS split out of inline blocks, July 2026.
- [neely/hiking-journal-proxy](https://github.com/neely/hiking-journal-proxy) — the Cloudflare Worker holding the PAT server-side for the above. See Pattern 7 for the general shape.
- [neely/hiking-journal-template](https://github.com/neely/hiking-journal-template) — the public, no-personal-data template. Ships with a demo entry (Hillary and Norgay's first ascent of Everest, 29 May 1953), README screenshots of the main page/expanded card/edit screen, and a documented optional "Going Private" walkthrough. Still uses the simpler browser-PAT write path described above, not the Worker proxy.

**Constraints (beyond global)**
- The base pattern's PAT has `repo` scope — full read/write to the repo. It's powerful; treat it like a password. (The live instance uses a fine-grained, Contents-only PAT instead, held server-side — see below.)
- In the base pattern, the PAT is stored in `localStorage` on each device — not secure against someone with physical access to the browser, but never exposed publicly. The live instance eliminates this entirely by moving the PAT into a Cloudflare Worker secret (Pattern 7's shape); the browser never holds a credential.
- GitHub Actions is unlimited for public repos; private repos have a 2,000 minutes/month free tier. The image processing Action is lightweight — typically under 30 seconds per run.
- Cloudflare Pages introduces a small propagation delay between a GitHub commit and the file being visible at the custom domain — true whether the repo is public or private, since reads are always same-origin now. The browser polling loop in `add.html` accounts for this.
- The concurrency block in the Action prevents race conditions when photos are uploaded in multiple bursts; a `git pull --rebase` at the start of the Action handles commit timing edge cases.

**Limitations**
- **Write latency** — saving a hike with photos takes 1–3 minutes: commit staging files one by one via API → commit `hikes.json` → wait for the Action (~30–60s) → poll for processed images.
- **Single writer** — no conflict resolution. If two devices save simultaneously, the second commit will fail or overwrite the first.
- **No real auth** — the PAT is all-or-nothing `repo` scope. No roles (viewer vs. editor), no per-user access. Cloudflare Access (if enabled) controls who can *reach* the site at all, but doesn't add roles within it.
- **Deletion is incomplete** — deleting a hike removes it from `hikes.json` but leaves image files in `assets/images/`. The repo grows even if entries are deleted.
- **GitHub as a database has limits** — the Contents API can't efficiently query or filter `hikes.json`. For hundreds of entries, parsing the whole file every page load becomes slow.
- **No offline support** — reads require a network call. No service worker or caching layer.

**How it could be upgraded**
- Add a `manifest.json` and service worker to make it a full PWA with offline reading.
- Split `hikes.json` into per-year files or a file-per-hike to reduce payload as the journal grows.
- Add drag-to-reorder on the photo upload list so hero shot selection is explicit.
- ~~Add a Cloudflare Worker as a thin proxy for both reads and writes to hide the PAT entirely from the browser~~ — done, July 2026, on the live instance ([neely/hiking-journal-proxy](https://github.com/neely/hiking-journal-proxy)). Worth being honest that this was mainly a convenience/tidiness win, not a material security jump — the repo was already private and the domain already Access-gated before this, so the PAT's browser exposure was already low-consequence. Only worth doing when the app is already Access-gated so a Worker's Route can piggyback on the existing Access application without extra setup.

---

### Pattern 4: GitHub as Database — Multi-Audience, PAT-Gated Write, No Pipeline

**What it is**
A refinement of Pattern 3, stripped of the image processing pipeline and optimized for a two-audience app: a read-only view for one group (kids) and a PAT-gated admin interface for another (parents). Key evolutions over Pattern 3: `sessionStorage`-only PAT handling, fine-grained PAT scoping, and optimistic UI updates that bypass CDN propagation delay. Currently running private, behind Cloudflare Access.

**How it works**
Two HTML files, two JSON files, one GitHub repo:

- **`index.html` (kid dashboard)** — fetches `ledger.json` and `investments.json` relative to its own domain — same-origin, no auth needed, no dependency on the repo being public. Computes balances on the fly by walking transactions sorted by date then sequence number — balances are never stored, always derived. Shows per-kid cards, transaction history with relative dates ("3 days ago"), date range filters, and live search. Designed to be bookmarked by kids.
- **`bank.html` (parent admin)** — on every new browser session, a fullscreen prompt requires a GitHub PAT before anything renders. The token is validated against the GitHub API before the app loads. Stored in `sessionStorage` only — clears when the tab closes, never persists to `localStorage`. Add, edit, and delete transactions via bottom sheets. Update investment balances. All writes use the GitHub Contents API (fetch current SHA → patch in-memory ledger → PUT back) — this already worked identically on a private repo, since it was never a public/anonymous read to begin with. The UI updates immediately from memory rather than re-fetching from the CDN, which sidesteps propagation delay entirely.
- **`ledger.json`** — a flat array of all transactions for all kids. Balances are computed at runtime, never stored. Git commit history is the audit log — every write creates a descriptive commit (`Add transaction for Adam`, `Delete transaction adam-1 for William`).
- **`investments.json`** — a separate flat array for investment account balances (SPY, Roth IRA, etc.) tracked alongside but excluded from spendable balance.
- **Hosting** — Cloudflare Pages + custom domain via CNAME at the domain registrar. No build step. Repo is private; both the custom domain and the `.pages.dev` fallback are gated with Cloudflare Access (email + one-time PIN).

**Examples**
- [neely/kid-bank](https://github.com/neely/kid-bank) — "Neely Bank": a family banking ledger for tracking kids' allowances, spending, and investments. Two kids, two audiences, one JSON file. Built 2026. Private repo, gated with Cloudflare Access on both domains.

**Constraints (beyond global)**
- Fine-grained PAT scoped to Contents read/write only — meaningfully tighter than the broad `repo`-scope PAT in Pattern 3.
- `sessionStorage` only for the PAT — clears when the tab closes. On a phone, Face ID or device passcode protects the saved keychain entry.
- No GitHub Action — writes go directly to the final JSON files, so there's no staging step or propagation delay to wait out on the write side.
- Cloudflare CDN propagation delay still exists for the `index.html` view (same-origin read, same as Pattern 3), but the admin `bank.html` updates its UI from memory immediately.

**Limitations**
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
- A public, no-personal-data template for this pattern (mirroring `hiking-journal-template`) is planned but not started.

> **Where Hyde fits:** [Hyde](https://github.com/neely/hyde) — the Jekyll post composer covered in [this post](/Building-a-Mobile-First-Post-Composer-for-Jekyll/), live on [neely.github.io](https://neely.github.io) — is a close cousin of this pattern: GitHub as database, fine-grained PAT-gated write, no processing pipeline. The difference is what's being written (Markdown posts and front matter instead of JSON ledger rows), but the mechanics — Contents API, SHA-based updates, no server — are the same shape.

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
- [neely/kb-apps](https://github.com/neely/kb-apps) (private) — `iron-tide.html`: Iron Tide, an 8-week double kettlebell program (ABC complex + carries on Day A; press ladder + snatches + push/pull on Day B). EMOM arc timer, phase navigation, real-time round logging, session summary. PWA-ready with apple-touch-icon. Live at [kb-apps.benneely.com](https://kb-apps.benneely.com).
- Same repo — `workout-dashboard.html`: a read-only analytics dashboard aggregating sessions from all programs into a single view with tonnage charts, heatmap, and session history.

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

## Tier 2 — GitHub as the Backend (continued)

---

### Pattern 7: Cloudflare Worker as Full Backend Proxy + Scheduled Action Pipeline

**What it is**
An evolution of Patterns 3 and 4 that removes the PAT from the browser entirely. Instead of the client holding a GitHub token (in `localStorage` or `sessionStorage`) and calling GitHub's API directly, a Cloudflare Worker sits between the browser and *every* external API — GitHub's Contents API and any third-party services — holding every secret server-side. Paired with a scheduled GitHub Action that runs independently of any browser session, aggregating third-party data and filing GitHub Issues as an ad-hoc notification channel.

**How it works**
- **The client (`app.js`)** — one HTML/JS app with no PAT prompt anywhere. Every read and write goes through the Worker's own routes (`/api/data/{key}` for GET/PUT against JSON files in the repo, `/api/steam-*` and `/api/itad-*` for proxied third-party calls). The browser holds zero secrets — auth to *the app* (Cloudflare Access, email OTP) is entirely separate from auth to the *data*, which the person never touches or could leak by pasting a token somewhere.
- **The Worker (`worker/steam-proxy.js`)** — a single Cloudflare Worker holding three secrets: `GITHUB_PAT` (fine-grained, Contents read/write only — same shape as Pattern 4's PAT, but living server-side instead of in the browser), plus two third-party API keys. A generic `DATA_PATHS` map routes any `/api/data/{key}` request to a specific file in the repo, so adding a new synced file is a one-line config change, not a new route. Each third-party proxy route adds its own key server-side before forwarding, so the browser's outbound calls never carry a vendor credential either.
- **Cloudflare Access (the auth layer)** — sits in front of the whole site, gating it with email OTP. This replaces the "PAT prompt as login" pattern from Patterns 3/4 entirely: the person authenticates to the app, not to GitHub.
- **The scheduled Action (weekly cron)** — two scripts run in sequence, independent of any browser session ever being open. The first calls a third-party API directly (its own Actions secret, a *separate* secret store from the Worker's copy of the same key — they don't share, so the same credential value has to be pasted into both places by hand) to refresh source-of-truth data, then commits the results directly via git using the Action's own token — no Worker involved for this write path at all. The second script checks the refreshed data against alert criteria and **files a GitHub Issue per newly-qualifying hit**, using GitHub's own Issues + notifications as the push channel instead of standing up an email/SMS service. De-duplication uses a small JSON ledger file committed alongside the other data, keyed however makes sense for the criterion (a price, an ID, a date) — so a qualifying condition that holds steady only alerts once, and a genuine state change alerts again.
- **Split write ownership, by file, not uniform** — some JSON files are written *only* by the scheduled Action (bypassing the Worker, straight to git); others are written by *both* the Action and live user-driven actions (through the Worker's PUT route); others are written live-only and never touched by the Action. Matched deliberately to how fresh each piece of data actually needs to be and who's the authoritative writer for it, rather than one blanket sync policy for everything.

**Examples**
- [neely/deckhand](https://github.com/neely/deckhand) — a personal Steam library/wishlist/queue tracker: live price data, a weekly-Action-driven playtime dashboard, and automated deal alerts (historic-low price, bundle value) filed as GitHub Issues. Live at [deckhand.benneely.com](https://deckhand.benneely.com). Built July 2026.

**Constraints (beyond global)**
- Requires standing up an actual Cloudflare Worker (not just Pages) — a real step up in setup complexity from Patterns 3/4's zero-backend approach, though still free-tier with nothing to manage day-to-day.
- Two separate secret stores for anything both the Action and the Worker need — GitHub Actions secrets and Cloudflare Worker secrets are entirely disconnected, so shared credentials drift apart unless kept in sync by hand.
- Cloudflare Access needs its own setup (a Cloudflare Zero Trust team), a separate moving piece from the Worker/Pages config — still free-tier for a handful of users.
- GitHub Issues as a notification channel only works if the person already watches the repo (for email/mobile push via GitHub's own app) — it's borrowed infrastructure, not a general-purpose alert system.

**Limitations**
- **Still single-writer, same as Patterns 3/4** — no real conflict resolution if a live write and a scheduled Action write land in the same moment; last-write-wins.
- **The Worker becomes a real dependency** — Patterns 3/4 have zero backend to keep running at all; this pattern has one small stateless service that gates every read and write, so a Cloudflare outage takes the whole app down, not just the write path.
- **De-dupe ledgers are a hand-rolled pattern, not a shared abstraction** — each new alert-style criterion needs its own ledger file and its own comparison logic; nothing reusable yet.
- **Issue-based alerts don't scale past "a repo one person watches"** — fine for personal use, wouldn't generalize to multiple recipients without a real notification service behind it.

**How it could be upgraded**
- Add a lightweight secret-sync step (or at minimum a documented note) so Action/Worker secret pairs don't silently drift out of sync.
- Generalize the de-dupe ledger into a small shared helper (`hasAlerted(key, value)` / `markAlerted(key, value)`) reusable across every alert type instead of hand-rolling each one's comparison logic.
- Replace GitHub Issues with a proper push channel (a Worker-triggered webhook to Discord/ntfy/a phone notification service) if this needs to support more than one recipient.
- Add a `/health` route on the Worker so an outage is visible on its own, before it's discovered by a failed write.

---

### Pattern 8: Static Broadcast App — Read-Only Runtime, Claude as the Write Agent, Worker as Stream Proxy

**What it is**
A fully static, read-only web app where no user ever authenticates or writes anything at runtime. The entire codebase — HTML, CSS, JS, station data, and image assets — is committed once and served forever. A Cloudflare Worker handles a specific browser constraint (CORS on third-party audio streams) rather than auth or data routing. All writes during development were made directly by Claude via the GitHub Contents API using a short-lived fine-grained PAT, from a phone, with no laptop involved at any point.

**How it works**
One HTML file is the entire app. It contains all styles, logic, station data (as a JS array), and references to self-hosted logo assets in an `icons/` folder. There is no JSON database, no user-writable layer, no login, and no runtime write path of any kind. The app plays audio, tracks which station is active in memory, and reacts to user input — all transiently, all in the browser, nothing persisted.

- **`index.html` (the whole app)** — a single file holding ~1150 lines: CSS custom properties for the color palette, a rotary drum tuner UI with orange needle and tick marks, a card grid for all 15 stations, vanilla JS for playback and accent color reactivity, and the full station roster as a JS array. Each station entry carries a name, frequency, stream URL, genre tags, city, accent color (pixel-sampled from the logo), logo path, donate URL, and two deferred-but-ready fields (`eyebrow`, `desc`) for a future detail view.
- **`icons/` (self-hosted logos)** — 15 PNGs, one per station. Each was sourced from a screenshot (station website, Instagram, or app icon), pasted into the Claude chat on a phone, processed by Claude (center-crop → circular mask → 128×128px), and committed directly to the repo. Accent colors were pixel-sampled from each processed image and hardcoded into the station data array.
- **The Cloudflare Worker (stream proxy)** — handles one specific browser constraint: some station stream URLs don't include CORS headers, which would block a browser from playing them directly. The Worker acts as a passthrough proxy, adding the necessary headers. It holds no secrets and performs no auth — it exists purely to satisfy the browser's CORS policy on audio streams.
- **Stream verification** — every stream URL in the station data was verified before being committed. Sources: [deroverda/recommended-radio-streams](https://github.com/deroverda/recommended-radio-streams), each station's own site, or direct DevTools extraction. Stations without a confirmed public HTTPS-embeddable stream are held in a footer link-out section, not in the playable roster.
- **No user auth, ever** — there is no PAT prompt, no login, no `localStorage`, no `sessionStorage`. The app is fully public and fully anonymous.

**The development pattern**
All writes during development — HTML edits, station data updates, icon commits, PLAN.md and NOTES.md maintenance — were made by Claude directly via the GitHub Contents API using a short-lived fine-grained PAT (scoped to Contents read/write on this repo only, pasted into chat, revoked after each session). The developer never opened a laptop. Testing happened on the same phone used to build it, at the live Cloudflare Pages URL, after every push. This made mobile layout issues immediately visible — tap target sizes, sticky header behavior, scroll behavior on station navigation — rather than discovered after the fact. This was also the project where the session-continuity system (see [Development Workflow](#development-workflow) below) went from "template I'm using" to "the only way I work now" — every phase of radio ran end-to-end from chat, no editor open at any point.

**Examples**
- [neely/radio](https://github.com/neely/radio) — "The Dial": a web radio tuner with 15 community, college, and public radio stations. Rotary drum navigation, real logos, pixel-sampled accent colors per station, donate links, responsive mobile layout. Live at [radio.benneely.com](https://radio.benneely.com). Built July 2026.

**Constraints (beyond global)**
- Repo is public — station data, accent colors, and all assets are visible to anyone. Appropriate for a broadcast-style app with no user data; not for anything sensitive.
- The Cloudflare Worker is a real dependency for stations whose streams require CORS proxying — if the Worker goes down, those streams stop playing. Stations with native CORS headers play directly and are unaffected.
- Fine-grained PAT used during development only, never at runtime. Treat each session's PAT as ephemeral — paste, use, revoke.
- `icons/` assets are self-hosted PNGs. Logo rights belong to each station — appropriate for personal/fan use, not commercial redistribution.
- Three stations (WUOG, WTUL, WXYC) use HTTP-only streams, blocked by the browser's mixed-content policy even through the Worker. Held in the footer as link-outs until a solution exists.

**Limitations**
- **No user state** — tuned station, volume, playback position reset on every page load. Intentional for a broadcast-style app; would need `localStorage` to add "resume where I left off."
- **No swipe gesture on the drum** — arrow buttons only. Acceptable for the current roster size; natural candidate to add if the roster grows.
- **Stream breakage is silent** — if a station changes its stream URL, the app plays nothing with no visible error beyond the status indicator on the card. No automated monitoring.
- **Station roster is static** — adding a station requires a commit. No admin UI, no user suggestions surfaced in-app (routed to GitHub Issues instead).
- **Accent colors are hardcoded** — pixel-sampled once at build time. If a station rebrands, the color has to be re-sampled manually.
- **Two deferred data fields** (`eyebrow`, `desc`) are populated but not rendered — waiting on a UX reason to surface them (tooltip, expand, detail panel).

**How it could be upgraded**
- Add `localStorage` to remember the last-tuned station and restore it on load.
- Add touch swipe handlers on the drum track for more natural mobile navigation.
- Add a GitHub Action that periodically pings each stream URL and files an Issue if one goes dead — automated stream health monitoring.
- Promote the three HTTP-only stations to the playable roster via a Worker that upgrades the stream to HTTPS in the proxy layer.
- Surface the `eyebrow` and `desc` fields in a card expand or hover detail panel — the copy is already written and ready.
- Add keyboard navigation (left/right arrows) to the drum tuner for desktop users.

### Pattern 9: Cloudflare Worker Deployed via GitHub Actions — Phone-Only CI/CD

**What it is**
A Cloudflare Worker whose deploy step runs in GitHub Actions rather than
locally via `wrangler` or through Cloudflare's own git-integration
auto-build. The distinguishing feature is the deploy *mechanism*, not the
runtime architecture — this pairs naturally with Pattern 7 (Worker as
backend), but the CI layer is what makes deploys possible with zero laptop
and zero local `wrangler` install at any point, matching the phone-only
development pattern (see Development Workflow below) all the way through
to shipping.

**How it works**
`wrangler.toml` is checked into the repo, but only its non-secret fields
(`name`, `main`, `compatibility_date`) — account ID and any Worker secrets
never live in that file. A `.github/workflows/deploy.yml` runs
`cloudflare/wrangler-action`, authenticated via a Cloudflare API token
scoped to Workers Scripts (Cloudflare's own "Edit Cloudflare Workers"
token template — broader than the bare minimum, but a maintained official
template beats hand-trimming permissions on a phone screen), stored as a
`CLOUDFLARE_API_TOKEN` repo secret alongside `CLOUDFLARE_ACCOUNT_ID`. The
trigger is `workflow_dispatch` (a manual tap in the GitHub app/mobile web),
not `push` — deliberately, since this pairs with the no-branches,
commit-straight-to-main habit (see Development Workflow), and a
push-triggered deploy would mean every commit ships immediately with no
gate. `workflow_dispatch` keeps one deliberate "ship it" moment while still
requiring nothing local.

**Examples**
- [neely/creel](https://github.com/neely/creel) — send-article-to-Kindle
  pipeline. First project built this way; deploy triggered via the GitHub
  API directly from a chat session (POST to the workflow's dispatch
  endpoint), no phone tap even required for that one. Live at
  `creel-worker.benaneely.workers.dev`. Built August 2026.

**Constraints (beyond global)**
- Cloudflare API tokens are account/zone-scoped, not per-Worker-script —
  there's no way to scope a token to "only this one Worker." A token with
  `Workers Scripts: Edit` can edit every Worker on the account. Factor this
  into which account a CI token gets created against, not into which repo
  it's added as a secret to.
- Unlike the fine-grained GitHub PATs used elsewhere in this workflow
  (session-scoped, pasted per-chat, never stored), this token has to
  persist as a GitHub secret for CI to keep working across sessions —
  "short-lived" isn't the right mitigation here. Narrow permissions +
  being able to revoke/rotate easily is the actual safeguard.

**Limitations**
- `workflow_dispatch` means a deploy doesn't happen automatically on
  commit — it's a separate, explicit step every time. Fine for a solo
  low-traffic project; would be a real friction point at higher deploy
  frequency.
- No staging/preview environment in this setup — every dispatch deploys
  straight to the one live Worker. Cloudflare's `versions upload` +
  gradual deploy exists as an escape hatch (see wrangler-action docs) but
  isn't wired up here.
- GitHub Actions' raw job logs redirect off `api.github.com` to Azure blob
  storage — outside typical sandboxed-agent network allowlists, so an
  agent helping with this pattern may not be able to fetch deploy output
  directly and will need the human to paste the expanded log section.

**How it could be upgraded**
- Switch to `push`-triggered deploys once a project's been stable a while
  and the "every commit ships" tradeoff feels acceptable.
- Add a lightweight pre-deploy check (even just `node --check` on the
  entry file) as a job step before the `wrangler-action` step, so a syntax
  error fails loud in CI instead of shipping.
- Promote the Cloudflare API token to an org-level secret once more than
  one project uses this pattern, instead of re-adding it per repo.

---

## Development Workflow

Everything above is about **where an app's data lives**. This section is a different axis entirely: **how the app actually gets built**, across many separate chat sessions with an AI assistant that has no memory between them — sometimes days or weeks apart, always starting cold. As of [deckhand](https://github.com/neely/deckhand) and [radio](https://github.com/neely/radio) (July 2026), this isn't a template being tried out — it's the only way new projects get built here, and for some projects (Pattern 8 above) it's fully phone-only: no laptop opens at any point.

Full write-up: [Solo Agent Context Kit](https://neely.github.io/agent-context-kit/). Template repo: [agent-context-project-template](https://github.com/neely/agent-context-project-template).

**The one principle:** every file exists to survive a cold start. The test for anything written into these files is whether a fresh chat, with zero memory of the project, would do the right thing from it alone — which is why *why we decided* and *what we rejected* matter as much as *what we did*.

### The five files

| File | Reader | Answers |
| ---- | ------ | ------- |
| `README.md` | A human arriving cold | What is this, where's it live, how do I run it |
| `AGENTS.md` | The agent | How should I behave, what's off-limits |
| `PLAN.md` | Agent + mid-build you | What's done, what's active, what's next (status block lives here, at the top — one home, never duplicated) |
| `NOTES.md` | Agent + mid-build you | Why is it built this way — decisions, dead-ends, "intentional not a bug" |
| `JOURNAL.md` | Future you | How did we get here, what was uncertain (append-only, newest on top) |

No `pointer.md`: a single-file "most recent summary" is a reasonable instinct, but the PLAN status block already does that job inline — a second file whose whole purpose is holding current state is one more thing that can drift out of sync. Skip it.

**Two optional additions, layered on top when they earn their place:**
- **`reference/`** — for *vendored* external material (API docs, a scraped spec, ported source) that doesn't belong in NOTES, which is reasoning and should stay skimmable. Must be distilled, not a raw dump — a 400-page spec pasted in whole is worse than a link. Read targeted, only when the task needs it.
- **A grounding spec** — a rung above AGENTS.md, for domains (scientific software especially) where field-level community consensus outranks any one project's preferences. Lives once, authoritatively, outside any single project, with a one-line hook in AGENTS.md deferring to it. No-op on projects without one. Example: [OmicsGrounding/proteomics-grounding](https://github.com/OmicsGrounding/proteomics-grounding).

### The session loop

- **Init** — read `AGENTS.md`, then PLAN's status block and active phase, skim NOTES for relevance, state the next step before writing any code. If PLAN left an embedded handoff prompt from last session, start there instead.
- **During** — targeted edits only, never a full-file rewrite for a few lines. Commit as you go, plain messages, straight to `main`, no branches, no squash, no prefixes — commits are the in-session safety net.
- **Shutdown** — update PLAN's status block, tick finished checkboxes, add new decisions/dead-ends to NOTES, sync README if it describes anything that changed, run the debrief into JOURNAL, then commit **and push**, reporting each step's result rather than a blanket "done." Push is the one that silently gets skipped most often.

### The debrief

Appended to the top of `JOURNAL.md` every session — Q1 and Q5 as a minimum, all five for a big session:
1. What are you least confident about, and what would prove it right or wrong?
2. What did you assume without stating it?
3. What's the biggest thing being missed?
4. What could've made this session more useful?
5. What would you suggest to improve?

### Marker conventions

The load-bearing part of the whole system — what stops a fresh agent from undoing settled work:
- **`(locked)`** — a settled decision, not to be reopened without being told to.
- **"don't relitigate" / "already decided"** — same, for bigger architectural calls.
- **"intentional, not a bug"** — negative-space documentation of what *not* to fix.
- **Dead-ends recorded as dead-ends** — "tried X, got Y, rolled back" stops re-exploration of a ruled-out path.
- **Embedded handoff prompt** — next session's kickoff written directly into PLAN at a clean stopping point; the highest-fidelity cold start available.

### The phone-only write pattern

The mechanical trick underneath Pattern 8 (and increasingly the default): give the agent a short-lived fine-grained PAT scoped to one repo, let it commit and push via the GitHub Contents API, and point a Cloudflare subdomain at the result — a webapp built and tested end-to-end without leaving the phone, including pasting screenshots in place of a file-save when the agent wants a directory it can't get. Worth being honest about the tradeoff: handing an LLM a PAT this way isn't ideal security practice, just a pragmatic one — paste, use, revoke, every session. (Mobile Codex/ChatGPT reportedly has a more native read/write GitHub integration; not yet evaluated here.)

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
| 2026-07-25 | Pattern 7: Cloudflare Worker as Full Backend Proxy + Scheduled Action Pipeline (deckhand)                            |
| 2026-07-25 | Pattern 8: Static Broadcast App — Read-Only Runtime, Claude as Write Agent, Worker as Stream Proxy (radio/The Dial) |
| 2026-07-25 | Added "A Note on How These Get Built: Session Continuity" — the agent-context-project-template workflow (AGENTS.md/PLAN.md/NOTES.md/JOURNAL.md) used across deckhand and radio                          |
| 2026-07-25 | Reorganized: moved Hyde cross-reference to sit under Pattern 4; promoted the session-continuity note to a full "Development Workflow" section at the end (five files, optional `reference/`/grounding layer, session loop, debrief, marker conventions, phone-only PAT pattern), cross-linked from Pattern 8; sourced from [Solo Agent Context Kit](https://neely.github.io/agent-context-kit/) |
| 2026-07-25 | Filled in missing link-outs: Pattern 6 examples now link the (private) kb-apps repo and kb-apps.benneely.com; Pattern 7 example now links deckhand.benneely.com                                     |
| 2026-07-26 | Rewrote Patterns 3 and 4: both no longer require a public repo for reads — `index.html` in each now reads relative to its own domain instead of `raw.githubusercontent.com`. Added "Available Templates" section (agent-context-project-template, hiking-journal-template; kid-bank template planned). Pattern 3 now documents the optional "Going Private" Cloudflare Access setup and links the public hiking-journal-template. Pattern 4 updated to reflect kid-bank now running private + gated. |
| 2026-07-30 | Pattern 3 updated: the live hiking-journal instance moved its write path off browser-`localStorage` PAT onto a Cloudflare Worker secret (Pattern 7's shape) — added [neely/hiking-journal-proxy](https://github.com/neely/hiking-journal-proxy), updated constraints/upgrade-ideas/file structure accordingly. Template repo unchanged, still uses the simpler browser-PAT version. |
| 2026-07-30 | Added "When to Graduate an App to Its Own Repo" — security/infrastructure/organizational/collaboration possibilities that unlock once a bundled sub-app (e.g. kb-apps' nine tools) splits into its own repo, using hiking-journal-proxy and hiking-journal-template as before/after examples. Includes a counter-note on when not to split. |
| 2026-07-30 | Fixed Global Constraints table — it previously claimed no server-side code, no backend, and no real secrets across all patterns, no longer true since Patterns 7-8. Added inline exceptions per row rather than rewriting the table. |
| 2026-07-30 | Added "What Splitting CSS/JS Out of Inline HTML Unlocks" — near-zero-effort wins (View Transitions, dark mode, print stylesheet) vs. genuine new capabilities (service worker, manifest.json/PWA install, Web Share, IndexedDB offline queue), plus a repo inventory of split vs. still-inline apps. kb-apps flagged as the next likely candidate — already has a shared `kb-lib.js` but no shared stylesheet. |
| 2026-07-30 | Corrected the kb-apps note above — `kb-lib.js` is deliberate template infrastructure for building new tools in the bundle, not organic duplication debt; removed the "next candidate" framing. Also cross-linked the Solo Agent Context Kit into "When to Graduate an App to Its Own Repo" — a freshly split repo is the right moment to scaffold it, not after the fact. |

