# FlockOS — AS-BUILT

> Complete system inventory as of **March 27, 2026**

A complete church management platform built on Google Apps Script and Google Sheets. Four API endpoints, 200 spreadsheet tabs, RBAC authentication, and a comprehensive deployment guide — everything needed to run a church CRM without a traditional server.

> *"I am the vine; you are the branches."* — John 15:5

---

## System Summary

| Metric | Value |
|--------|-------|
| **Total files on disk** | 95 |
| **Source JavaScript** | 21 files — 33,912 lines — 1,681,507 bytes |
| **Source HTML** | 13 files — 8,159 lines — 433,447 bytes |
| **Minified JavaScript** | 18 `.min.js` files — 1,114,819 bytes (Acts) |
| **Minified HTML + assets** | 14 files — 1,866,086 bytes (Revelation) |
| **Backend reference** | 5 combined `.gs` files — 29,211 lines (Database) |
| **Build tooling** | 3 files — 180 lines (Exodus) |
| **Learn More pages** | 7 HTML files — 778 lines (Romans) |
| **JS minification ratio** | 1.60 MB → 1.09 MB = **33.7% reduction** |

---

## Architecture

FlockOS uses four Google Sheets as databases, each exposed through a Google Apps Script Web App:

| API | Codename | Sheet | Tabs | Purpose |
|-----|----------|-------|------|---------|
| **FLOCK** | John | Flock CRM | 79 | Members, auth, pastoral care, services, songs, communications |
| **EXTRA** | Luke | Flock Statistics | 53 | Analytics, metrics, future expansion slots |
| **APP** | Matthew | Flock Content | 12 | Public content (devotionals, lexicon, quiz, theology) |
| **MISSIONS** | Mark | Flock Missions | 56 | Persecution data, country dossiers, mission teams |

**Total: 200 tabs across 4 databases**

The `.gs` backend files are deployed directly via the Apps Script editor — they are not committed to git. Combined reference copies live in `FlockOS-GS/Database/` for local documentation and schema work.

---

## Folder Structure (AS-BUILT)

```
FlockOS/                                 95 files on disk
│
├── index.html                           Public-facing site — auth-scoped navigation (22 lines)
├── Learn More.html                      Public learn-more landing (empty placeholder)
├── manifest.json                        PWA manifest (43 lines)
├── the_living_water.min.js              Service worker — minified (40 lines)
├── LICENSE                              Proprietary CRM + Free Learning Modules (44 lines)
├── README.md                            This file
├── .gitignore                           Excludes Genesis/, Exodus/, Database/, *.gs, *.txt, *.bak
├── .nojekyll                            GitHub Pages bypass
│
└── FlockOS-GS/
    │
    ├── Genesis/                         ⛔ .gitignored — 35 source files
    │   │
    │   │  ── JavaScript (21 files — 33,912 lines) ──
    │   │
    │   ├── the_tabernacle.js              10,497 lines — 48+ module renderers, Themes, Interface Studio
    │   ├── fine_linen.js                   5,379 lines — CSS theme system (13 themes) + Interface Studio
    │   ├── the_way.js                      3,013 lines — Learning Hub — 16-tab education dashboard
    │   ├── the_life.js                     2,378 lines — My Flock Portal — pastoral command hub
    │   ├── the_seasons.js                  2,199 lines — Calendar, Tasks & Check-In Hub
    │   ├── the_shofar.js                   1,621 lines — Song library, chord charts, PDF export
    │   ├── the_commission.js               1,398 lines — Deployment automation blueprint
    │   ├── the_true_vine.js                1,293 lines — Centralized API client (all 4 branches)
    │   ├── the_harvest.js                    920 lines — Ministry Hub — events, sermons, services, songs
    │   ├── the_wellspring.js                 859 lines — Local data layer — offline .xls import via IndexedDB
    │   ├── the_cornerstone.js                790 lines — Architecture registry (runtime-queryable)
    │   ├── the_shepherd.js                   771 lines — People Engine — member search/profile/3-step save
    │   ├── the_well.js                       559 lines — Google Drive sync for offline churches
    │   ├── the_trumpet.js                    486 lines — Notification system (admin push alerts)
    │   ├── firm_foundation.js                462 lines — Authentication guard
    │   ├── love_in_action.js                 347 lines — Care Hub — 4-tab care/prayer/compassion/outreach
    │   ├── the_scrolls.js                    307 lines — Interaction Ledger — 30+ pastoral touchpoint types
    │   ├── the_fold.js                       276 lines — Groups & Attendance — 2-tab interface
    │   ├── the_living_water.js               197 lines — Service worker (source)
    │   ├── the_pagans.js                     160 lines — Public-facing content helpers
    │   └── the_truth.js                        0 lines — Reserved placeholder (John 8:32)
    │   │
    │   │  ── HTML (13 files — 8,159 lines) ──
    │   │
    │   ├── the_pentecost.html              1,776 lines — Interactive deployment guide
    │   ├── the_good_shepherd.html          1,272 lines — Admin dashboard
    │   ├── index.html                        716 lines — Main app shell (Genesis copy)
    │   ├── fishing-for-men.html              695 lines — Value proposition document
    │   ├── the_gift_drift.html               527 lines — Spiritual Gifts 4-phase curriculum
    │   ├── the_wall.html                     512 lines — Login page
    │   ├── the_anatomy_of_worship.html       493 lines — Anatomy of Worship standalone page
    │   ├── the_call_to_forgive.html          480 lines — Call to Forgive standalone page
    │   ├── the_generations.html              460 lines — Generations of Faith standalone page
    │   ├── the_weavers_plan.html             408 lines — Weaver's Plan 8-chapter curriculum
    │   ├── prayerful_action.html             387 lines — Prayerful Action standalone page
    │   ├── the_invitation.html               255 lines — Invitation page
    │   └── Learn More.html                   178 lines — Learn More template
    │   │
    │   │  ── Backup ──
    │   │
    │   └── the_gift_drift.html.bak          Backup of Gift Drift page
    │
    ├── Acts/                              ✅ Git-tracked — 18 minified .min.js files (1,114,819 bytes)
    │   │
    │   ├── the_tabernacle.min.js            428,785 bytes
    │   ├── fine_linen.min.js                147,364 bytes
    │   ├── the_way.min.js                   109,674 bytes
    │   ├── the_life.min.js                   82,908 bytes
    │   ├── the_seasons.min.js                76,141 bytes
    │   ├── the_shofar.min.js                 48,448 bytes
    │   ├── the_cornerstone.min.js            34,492 bytes
    │   ├── the_well.min.js                   32,926 bytes
    │   ├── the_shepherd.min.js               32,403 bytes
    │   ├── the_true_vine.min.js              30,283 bytes
    │   ├── the_harvest.min.js                28,096 bytes
    │   ├── the_wellspring.min.js             17,026 bytes
    │   ├── love_in_action.min.js             12,914 bytes
    │   ├── the_fold.min.js                   10,206 bytes
    │   ├── the_scrolls.min.js                 9,442 bytes
    │   ├── the_trumpet.min.js                 7,123 bytes
    │   ├── firm_foundation.min.js             6,588 bytes
    │   └── the_pagans.min.js                      0 bytes (empty — source is 160 lines of comments only)
    │
    ├── Revelation/                        ✅ Git-tracked — 14 files (1,866,086 bytes)
    │   │
    │   │  ── Minified HTML (11 files) ──
    │   │
    │   ├── the_pentecost.min.html            76,196 bytes — Interactive deployment guide
    │   ├── the_good_shepherd.min.html        41,536 bytes — Admin dashboard
    │   ├── the_call_to_forgive.min.html      26,671 bytes — Call to Forgive page
    │   ├── the_weavers_plan.min.html         23,217 bytes — Weaver's Plan curriculum
    │   ├── the_anatomy_of_worship.min.html   20,521 bytes — Anatomy of Worship page
    │   ├── the_gift_drift.min.html           20,366 bytes — Spiritual Gifts curriculum
    │   ├── prayerful_action.min.html         16,702 bytes — Prayerful Action page
    │   ├── fishing-for-men.min.html          15,589 bytes — Value proposition document
    │   ├── the_generations.min.html          15,313 bytes — Generations of Faith page
    │   ├── the_invitation.min.html           14,503 bytes — Invitation page
    │   └── the_wall.min.html                 11,750 bytes — Login page
    │   │
    │   │  ── Minified JavaScript (1 file) ──
    │   │
    │   ├── the_commission.min.js             55,145 bytes — Deployment automation blueprint
    │   │
    │   │  ── Assets (2 files) ──
    │   │
    │   ├── FlockOS_Blue.png               1,031,106 bytes — Logo (blue variant)
    │   └── FlockOS_White.png                497,471 bytes — Logo (white variant)
    │
    ├── Romans/                            ✅ Git-tracked — 7 Learn More pages (778 lines)
    │   │
    │   ├── 1-Matthew.html                    106 lines — APP API overview
    │   ├── 2-Mark.html                       106 lines — MISSIONS API overview
    │   ├── 3-Luke.html                       113 lines — EXTRA API overview
    │   ├── 4-John.html                       133 lines — FLOCK API overview
    │   ├── 5-Acts.html                       140 lines — Acts folder overview
    │   ├── 6-Romans.html                      79 lines — Romans folder overview
    │   └── 7-Revelation.html                 101 lines — Revelation folder overview
    │
    ├── Database/                          ⛔ .gitignored — 9 reference files (42,825 lines)
    │   │
    │   │  ── Combined .gs reference copies (5 files — 29,211 lines) ──
    │   │
    │   ├── John_Combined.gs                24,045 lines — FLOCK API (all 26 .gs files combined)
    │   ├── Mark_Combined.gs                 2,445 lines — MISSIONS API (all 4 .gs files combined)
    │   ├── Duplicate_Databases.gs           1,742 lines — Database duplication utilities
    │   ├── Luke_Combined.gs                   598 lines — EXTRA API (all 4 .gs files combined)
    │   ├── Matthew_Combined.gs                381 lines — APP API (all 2 .gs files combined)
    │   │
    │   │  ── Schema & documentation (4 files — 13,614 lines) ──
    │   │
    │   ├── flockos_project_notes.md         8,968 lines — Project notes & decisions
    │   ├── flockos_schema.sql               2,254 lines — SQL schema definition
    │   ├── SCHEMA_AUDIT.md                  2,106 lines — Schema vs. code audit
    │   └── future_planning.md                 286 lines — Roadmap & future features
    │
    └── Exodus/                            ⛔ .gitignored — 3 build tool files (180 lines)
        │
        ├── minify.py                         138 lines — JS (rjsmin) + HTML (minify_html) minification
        ├── build.sh                           37 lines — Combined .gs deployment helper
        └── minify.sh                           5 lines — Build entry point (activates .venv, runs minify.py)
```

---

## Git-Tracked vs. Local-Only

| Scope | Folder / Files | Tracked | Notes |
|-------|---------------|---------|-------|
| Root | `index.html`, `Learn More.html`, `manifest.json`, `the_living_water.min.js`, `LICENSE`, `README.md`, `.nojekyll` | ✅ Yes | Public site + PWA manifest + service worker |
| Acts | 18 `.min.js` files | ✅ Yes | Production JavaScript bundle |
| Revelation | 11 `.min.html` + 1 `.min.js` + 2 `.png` | ✅ Yes | Production HTML + logos |
| Romans | 7 `.html` files | ✅ Yes | Learn More detail pages |
| Genesis | 21 `.js` + 13 `.html` + 1 `.bak` | ❌ .gitignored | Unminified source (development only) |
| Exodus | 3 build tool files | ❌ .gitignored | Build pipeline |
| Database | 5 `.gs` + 3 `.md` + 1 `.sql` | ❌ .gitignored | Backend references + schema docs |

---

## Quick Start

Full step-by-step instructions are in **the_pentecost.min.html** (in `Revelation/`) — open it in a browser for the best experience. The short version:

### 1. Create Google Sheets (4)
Create **Flock CRM**, **Flock Statistics**, **Flock Content**, and **Flock Missions** spreadsheets. Copy each Sheet ID.

### 2. Deploy FLOCK API (John)
1. Create a new Apps Script project at [script.google.com](https://script.google.com)
2. Add all `.gs` files from the John backend (reference copy in `Database/John_Combined.gs`)
3. Set Script Property: `SHEET_ID` = your Flock CRM Sheet ID
4. Run `setupExpansion()` → creates 79 tabs
5. Run `initPepper()` → generates encryption secret
6. Seed your admin user in AuthUsers + AccessControl tabs
7. Run `migrateAllPasswords()` → hashes the password
8. Deploy as Web App (Execute as: Me, Access: Anyone)

### 3. Deploy remaining APIs
Repeat for EXTRA (Luke), APP (Matthew), and MISSIONS (Mark) — see the_pentecost.html for details.

### 4. Configure frontend
Update your frontend's API configuration with the four deployment URLs.

---

## Authentication

- **Method**: Email + passcode
- **Hashing**: SHA-256 with per-user salt + server-side pepper
- **Sessions**: 6-hour TTL, stored client-side
- **RBAC**: 6 levels — readonly (0), volunteer (1), care (2), leader (3), pastor (4), admin (5)

---

## Key Features

- **Member Management** — Full CRUD with pastoral notes, tags, contact masking
- **Member Cards** — Sequential card numbers with configurable prefix (default ATOG-0001)
- **Member Name Dropdowns** — All assignment fields sitewide use member directory select dropdowns (Care, Compassion, Messages, Giving, Prayer)
- **Module Visibility** — Admin toggle switches to show/hide any module from public & admin navigation
- **Tasks Module** — Card grid with filters (status/priority/category), complete/archive/delete, recurring tasks, priority badges, category pills
- **Prayer Admin** — Card grid layout with full prayer text prominent, metadata as pills (status, category, confidential, follow-up, assigned, date), Reply per card
- **Prayer Request** — Public prayer submission + admin management with status, assignment & replies
- **Messages** — Card-based inbox/sent with avatar initials, time-ago dates, unread indicators. Full message detail view with read receipts, reply with quoted original, forward, SMS via native link, delete with confirmation
- **Notifications** — Topbar bell with unread count badge, dropdown panel with subject/body/time-ago, dismiss or mark-all-read, 30s polling, click-to-navigate routing for 20+ module types, mobile-responsive full-width panel
- **Journaling** — Personal journal for prayer, study & reflection (readonly+ access) with scripture linking
- **Bible.com Integration** — Scripture references link directly to bible.com ESV across all modules
- **Daily Devotional** — Unified daily spiritual life page: single-card date navigation, devotion (Scripture/Reflection/Question/Prayer), reading plan (OT/NT/Ps/Pr), inline journal entry (members only), and prayer request submission — all in one view
- **Control Panel (Admin Settings)** — All 8 sections are collapsible accordion panels (all closed by default) with summary counts/status in the trigger bar: Security, Identity & Branding, Display Preferences, Theme Administration, Visible Apps, System Settings, API Health, and Provisioning
- **Themes Module (Public)** — Dedicated module accessible to all users via Growth nav group. Personal theme picker with swatch grid (respects admin's Allow Custom Themes toggle). Full Interface Studio: 30+ font-size sliders, padding/spacing controls (including split V/H pairs), corner-radius sliders, shadow-intensity dropdown, body & heading font pickers (18 Google Fonts), custom CSS textarea. Live preview on every change; Save All persists to localStorage + backend AppConfig; Reset to Defaults clears everything; Restore Visuals to Default reverts unsaved changes
- **Theme Administration (Admin)** — In Control Panel: foundational theme dropdown + swatch row, Allow Custom Themes toggle. When disabled, personal themes and Interface Studio are locked for all users
- **API Health Dashboard** — In Control Panel: color-coded health pills (green/yellow/red) for all 4 API branches. Run Diagnostics button calls `TheVine.manifest.diagnostics()` and shows latency per branch. Pills also appear on each Provisioning API card
- **Health Pills** — CSS component (`.health-pill-ok/warn/fail/unknown`) in fine_linen.js with animated pulsing dots, wired to the theme's `--success/--warning/--danger` CSS variables
- **Dual Font Scale** — Separate desktop and mobile font-size sliders in Control Panel. Viewport-aware IIFE with `matchMedia` applies the correct scale
- **Provisioning** — Multi-endpoint load balancing: 4 APIs × 3 tiers (Primary/Secondary/Tertiary) = 12 URL fields with monospace `.prov-url-field` styling, plus 4 toggles (Primary On by default, Secondary Off, Tertiary Off, Randomization Off). `_resolveUrl()` builds a pool from enabled tiers and picks randomly or first; `_appTab()` does the same with sequential failover. Test All Endpoints pings each health endpoint. Saved to localStorage + backend AppConfig as `PROVISIONING_URLS` and auto-loaded on init via `TheVine.configure()`
- **Audit Logging** — All flock and prayer interactions are tracked with user, action, and timestamp
- **Service Planning** — Service order builder with item scheduling
- **Songs & Music Stand** — Song catalog, ChordPro chord charts, arrangement management, live setlist view, PDF lead-sheet export
- **Spiritual Care** — Case tracking, interaction logging, follow-up management, member name assignment
- **Communications** — Email/SMS templates, campaign tracking, delivery logs
- **Compassion & Outreach** — Benevolence fund, needs tracking, outreach campaigns, member name assignment
- **Discipleship & Learning** — Growth paths, milestones, courses, quizzes
- **World Missions** — 48 country dossiers, partner tracking, team management
- **Volunteers** — Schedule & team assignments, member directory name lookup, vCard download links
- **Statistics** — Analytics dashboards, attendance, giving, growth metrics
- **Multi-Church** — Campus management, shared resources (expansion-ready)
- **iOS Mobile** — All inputs 16px to prevent Safari auto-zoom, lightened input backgrounds, responsive modals, root font-size: 100% for user font scaling
- **Local Data Mode (TheWellspring)** — Offline-capable data layer for churches without reliable internet. Import .xls/.xlsx files via the browser, stored in IndexedDB, served through TheVine's resolver hook. Four springs map to the four databases. Toggle, import, export, and status controls in Settings Data Source panel. Cloud-hosted interface remains unchanged; only the data source changes
- **Google Drive Sync (TheWell)** — Connects to Google Drive so church data stored as .xlsx files can sync automatically. Built on top of TheWellspring. OAuth 2.0 browser-only flow, configurable auto-sync interval, push local changes back to Drive
- **Learning Hub (TheWay)** — Consolidated growth portal: 16 tabs covering courses, quizzes, reading plans, theology, lexicon, apologetics, counseling, devotionals, certificates, and analytics. Dashboard with KPIs, streak tracking, continue-learning, recommendations. Standalone HTML curriculum pages (Gift Drift, Weaver's Plan, Generations, Anatomy of Worship, Prayerful Action) linked from scrollable card row with smart `?from=public|admin` back-link routing. Course quizzes sample 12 random questions from the pool
- **Heart Check** — Self-assessment diagnostic in Learning Hub and module renderers. Yes = danger (red), No = healthy (green). Results logged silently to localStorage for pastoral context
- **Silent Diagnostic Logging** — Quiz scores and Heart Check results are stored in `localStorage` under `flockos_diagnostics`. When a user submits a prayer request, the most recent diagnostic snapshot is automatically appended as a `--- Spiritual Context (auto) ---` block, giving pastors insight without requiring the user to self-report
- **Ministry Hub (TheHarvest)** — Events, sermons, service plans, songs, ministry teams, and volunteer scheduling consolidated into one dashboard. KPI ribbon, upcoming events timeline, donut charts, quick actions
- **Calendar Hub (TheSeasons)** — Unified calendar with month/week/day/agenda views, personal events, iCal feeds, recurrence engine, task management, and attendance check-in. 7-tier role-based event visibility
- **My Flock Portal (TheLife)** — Pastoral command hub with dashboard-of-apps pattern: People, Love in Action, The Fold, Activity. Full-page editors for members, care cases, prayer, compassion, outreach
- **Admin Dashboard** — KPI ribbon, scratchpad, quick tasks, font size controls (75–150%), quick action links
- **Tools Launchpad** — Card grid of 27 app launchers covering all major modules
- **Auth-Scoped Navigation** — Public site sidebar uses `data-auth` attributes to show/hide nav items based on authentication status: public (always visible), required (logged-in users), admin (admin role only)
- **Per-User Module Permissions** — GRANT/DENY overrides per user via Permissions sheet. `Nehemiah.canAccess(moduleKey)` for frontend gating, `requireModule()` for API enforcement

---

## Frontend File Map

Each source file in `Genesis/` has a specific role in the frontend architecture:

| Source (Genesis) | Minified Output | Lines | Bytes (min) | Role |
|-----------------|----------------|------:|------------:|------|
| `the_tabernacle.js` | `Acts/the_tabernacle.min.js` | 10,497 | 428,785 | Core renderer — 48+ module UIs, Themes, Interface Studio |
| `fine_linen.js` | `Acts/fine_linen.min.js` | 5,379 | 147,364 | CSS theme system (13 themes) + Interface Studio styles |
| `the_way.js` | `Acts/the_way.min.js` | 3,013 | 109,674 | Learning Hub — 16-tab education dashboard |
| `the_life.js` | `Acts/the_life.min.js` | 2,378 | 82,908 | My Flock Portal — pastoral command hub |
| `the_seasons.js` | `Acts/the_seasons.min.js` | 2,199 | 76,141 | Calendar, Tasks & Check-In Hub |
| `the_shofar.js` | `Acts/the_shofar.min.js` | 1,621 | 48,448 | Song library, chord charts, Music Stand, PDF export |
| `the_commission.js` | `Revelation/the_commission.min.js` | 1,398 | 55,145 | Deployment automation blueprint |
| `the_true_vine.js` | `Acts/the_true_vine.min.js` | 1,293 | 30,283 | Centralized API client (all 4 branches) |
| `the_harvest.js` | `Acts/the_harvest.min.js` | 920 | 28,096 | Ministry Hub — events, sermons, services, songs |
| `the_wellspring.js` | `Acts/the_wellspring.min.js` | 859 | 17,026 | Local data layer — offline .xls import via IndexedDB |
| `the_cornerstone.js` | `Acts/the_cornerstone.min.js` | 790 | 34,492 | Architecture registry (runtime-queryable) |
| `the_shepherd.js` | `Acts/the_shepherd.min.js` | 771 | 32,403 | People Engine — member search/profile/3-step save |
| `the_well.js` | `Acts/the_well.min.js` | 559 | 32,926 | Google Drive sync for offline churches |
| `the_trumpet.js` | `Acts/the_trumpet.min.js` | 486 | 7,123 | Notification system (admin push alerts) |
| `firm_foundation.js` | `Acts/firm_foundation.min.js` | 462 | 6,588 | Authentication guard |
| `love_in_action.js` | `Acts/love_in_action.min.js` | 347 | 12,914 | Care Hub — 4-tab care/prayer/compassion/outreach |
| `the_scrolls.js` | `Acts/the_scrolls.min.js` | 307 | 9,442 | Interaction Ledger — 30+ pastoral touchpoint types |
| `the_fold.js` | `Acts/the_fold.min.js` | 276 | 10,206 | Groups & Attendance — 2-tab interface |
| `the_living_water.js` | `the_living_water.min.js` (root) | 197 | — | Service worker |
| `the_pagans.js` | `Acts/the_pagans.min.js` | 160 | 0 | Public-facing content helpers (comments only) |
| `the_truth.js` | *(no output)* | 0 | — | Reserved placeholder (John 8:32) |

---

## Backend Reference Files (Database/)

The `.gs` files are deployed directly via the Apps Script editor. These combined reference copies exist locally for documentation, schema auditing, and future Cloud SQL migration work:

| File | Lines | Backend |
|------|------:|---------|
| `John_Combined.gs` | 24,045 | FLOCK API — 26 .gs files: entry point, auth, routing, setup, hashing, database helpers, utilities, expansions, and 18 module files |
| `Mark_Combined.gs` | 2,445 | MISSIONS API — 4 .gs files: router, shared utils, 50 handler functions, setup (56 tabs) |
| `Duplicate_Databases.gs` | 1,742 | Database duplication utilities |
| `Luke_Combined.gs` | 598 | EXTRA API — 4 .gs files: router, shared utils, 27 handler functions, setup (53 tabs) |
| `Matthew_Combined.gs` | 381 | APP API — 2 .gs files: entry point + setup (12 tabs) |
| `flockos_schema.sql` | 2,254 | SQL schema definition (200 tabs) |
| `flockos_project_notes.md` | 8,968 | Project notes & architectural decisions |
| `SCHEMA_AUDIT.md` | 2,106 | Schema vs. code consistency audit |
| `future_planning.md` | 286 | Roadmap & future features |

---

## Minification & Build

FlockOS ships every JavaScript and HTML file in two forms:

| Form | Example | Location | Purpose |
|------|---------|----------|---------|
| **Source JS** | `the_tabernacle.js` | Genesis/ | Human-readable, fully commented (development) |
| **Minified JS** | `the_tabernacle.min.js` | Acts/ | Whitespace + comments stripped (production) |
| **Source HTML** | `the_gift_drift.html` | Genesis/ | Readable standalone pages with Tailwind + Chart.js |
| **Minified HTML** | `the_gift_drift.min.html` | Revelation/ | HTML minified via Rust-backed `minify_html` |

### Minification Results (AS-BUILT)

| Category | Source | Minified | Reduction |
|----------|-------:|--------:|---------:|
| **JavaScript (Acts)** | 1,681,507 bytes | 1,114,819 bytes | **33.7%** |
| **HTML (Revelation)** | 433,447 bytes | 337,509 bytes | **22.1%** |

### Why Minify?

- **~34% smaller JS payload** — the full Acts bundle drops from 1.60 MB to 1.09 MB
- **Faster time-to-interactive** — smaller files parse and execute faster, especially on mobile devices and lower-bandwidth connections common in mission-field deployments
- **Consistent cross-platform loads** — reduced file sizes smooth out performance differences between desktop Chrome, mobile Safari (iOS), Android WebView, and other browsers
- **No dependency risk** — minification is handled by `rjsmin` (JS) and `minify_html` (HTML) — two lightweight Python packages with no npm, Webpack, or framework toolchain required
- **Long-term reliability** — minified output is deterministic and reproducible. The build pipeline has zero network dependencies at build time, runs in under 2 seconds, and produces byte-identical output on any machine with the same Python venv

### Build Workflow

```bash
# From the FlockOS root:
./minify.sh
```

This activates the project's Python virtual environment (`.venv/`) and runs `FlockOS-GS/Exodus/minify.py`, which:

1. Reads every `.js` source file from `FlockOS-GS/Genesis/`
2. Writes each minified output as a `.min.js` file into `FlockOS-GS/Acts/`
3. Minifies the service worker (`the_living_water.js`) to the project root
4. Minifies standalone HTML pages from `Genesis/` into `Revelation/` using `minify_html`
5. Prints a per-file size comparison and total reduction percentage

### Development vs. Production

During development, you edit the readable source files in `Genesis/` (e.g., `the_tabernacle.js`). When you are ready to deploy, run the minifier — it reads from `Genesis/`, writes `.min.js` to `Acts/` and `.min.html` to `Revelation/`. The HTML pages already reference the `Acts/*.min.js` versions, so no further changes are needed. Only `Acts/`, `Revelation/`, and `Romans/` are committed to git; `Genesis/`, `Exodus/`, and `Database/` are .gitignored.

### Build Tools (Exodus/)

| File | Lines | Purpose |
|------|------:|---------|
| `minify.py` | 138 | JS minification via `rjsmin`, HTML minification via `minify_html` |
| `build.sh` | 37 | Combined .gs deployment helper |
| `minify.sh` | 5 | Build entry point — activates `.venv/`, runs `minify.py` |

---

## Music Stand Module

The frontend Music Stand (`FlockOS-GS/Genesis/the_shofar.js` — 1,621 lines) provides:
- Searchable song library with full CRUD
- Arrangement management with ChordPro chord notation
- Live Music Stand view — load a service plan, navigate songs with arrow keys
- PDF export — single arrangement or full setlist via jsPDF

Entry point: `window.openMusicStandApp()`

---

## License

**CRM Software** — Proprietary. Use, deployment, and modification require written permission from the author. Licensing available for churches and organizations upon request.

**Learning Modules** — Free for personal, church, and educational use.

See [LICENSE](LICENSE) for full terms.
