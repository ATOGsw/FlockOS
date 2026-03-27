# FlockOS

A complete church management backend built on Google Apps Script and Google Sheets. Four API endpoints, 200 spreadsheet tabs, RBAC authentication, and a comprehensive deployment guide — everything needed to run a church CRM without a traditional server.

> *"I am the vine; you are the branches."* — John 15:5

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

## Folder Structure

```
FlockOS/
├── FlockOS-GS/                        55 source files (36 .gs + 19 .js)
│   ├── 1-Matthew/                     APP API (public content) — 2 .gs files
│   │   ├── Matthew.gs                   Entry point (doGet: health check + tab reader)
│   │   └── Truth.gs                     setupAppApi() — creates 12 content tabs
│   │
│   ├── 2-Mark/                        MISSIONS API — 4 .gs files
│   │   ├── Code.gs                      doGet router + auth validation via John
│   │   ├── Shared.gs                    Shared utilities
│   │   ├── Missions.gs                  50 handler functions (missions action routing)
│   │   └── Gospel.gs                    setupMissionsApi() — creates 56 tabs
│   │
│   ├── 3-Luke/                        EXTRA API (statistics) — 4 .gs files
│   │   ├── Code.gs                      doGet router + auth validation via John
│   │   ├── Shared.gs                    Shared utilities
│   │   ├── Statistics.gs                27 handler functions (statistics action routing)
│   │   └── Extra.gs                     setupExtraApi() — creates 53 tabs
│   │
│   ├── 4-John/                        FLOCK API (CRM) — 26 .gs files
│   │   ├── John.gs                      Entry point (doGet/doPost + core utilities)
│   │   ├── Auth.gs                      Authentication & session management
│   │   ├── Api.gs                       Action routing layer
│   │   ├── Setup.gs                     setupExpansion() — creates 79 tabs
│   │   ├── Hash.gs                      SHA-256 salt+pepper hashing
│   │   ├── Database.gs                  Sheet read/write helpers
│   │   ├── Utilities.gs                 Shared utilities
│   │   ├── Expansions.gs               Multi-church architecture
│   │   └── ...                          18 module files (Cards, Care, Songs, etc.)
│   │
│   ├── Genesis/                     Source JavaScript + HTML (~30,000 lines JS + 11 HTML pages) ⛔ .gitignored
│   │   ├── the_true_vine.js             Centralized API client (all 4 branches)
│   │   ├── firm_foundation.js           Authentication guard
│   │   ├── fine_linen.js               CSS theme system (13 themes) + Interface Studio
│   │   ├── the_tabernacle.js           48+ module renderers + Themes + Interface Studio (~9,600 lines)
│   │   ├── the_seasons.js              Calendar, Tasks & Check-In Hub (~2,000 lines)
│   │   ├── the_life.js                 My Flock Portal — pastoral command hub (~2,200 lines)
│   │   ├── the_way.js                  Learning Hub — 16-tab education dashboard (~2,900 lines)
│   │   ├── the_harvest.js              Ministry Hub — events, sermons, services, songs (~900 lines)
│   │   ├── the_shofar.js              Song library, chord charts, PDF export
│   │   ├── the_shepherd.js            People Engine — member search/profile/3-step save
│   │   ├── love_in_action.js          Care Hub — 4-tab care/prayer/compassion/outreach
│   │   ├── the_fold.js                Groups & Attendance — 2-tab interface
│   │   ├── the_scrolls.js             Interaction Ledger — 30+ pastoral touchpoint types
│   │   ├── the_wellspring.js          Local data layer — offline .xls import via IndexedDB
│   │   ├── the_well.js                Google Drive sync for offline churches
│   │   ├── the_cornerstone.js         Architecture registry (runtime-queryable)
│   │   ├── the_truth.js               Reserved placeholder (John 8:32)
│   │   ├── the_trumpet.js             Notification system (admin push alerts)
│   │   └── the_pagans.js              Public-facing content helpers
│   │
│   ├── Exodus/                      Build tools ⛔ .gitignored
│   │   ├── minify.py                    JS + HTML minification script
│   │   └── build.sh                     Combined .gs deployment helper
│   │
│   ├── Acts/                        Production minified JavaScript — 18 .min.js files
│   │
│   ├── Romans/                      Learn More pages (one per folder)
│   │
│   └── Revelation/                  Production minified HTML + assets
│       ├── the_good_shepherd.min.html   Admin dashboard (minified)
│       ├── the_wall.min.html            Login page (minified)
│       ├── the_pentecost.min.html       Interactive deployment guide (minified)
│       ├── fishing-for-men.min.html     Value proposition document (minified)
│       ├── the_gift_drift.min.html      Spiritual Gifts 4-phase curriculum (minified)
│       ├── the_generations.min.html     Generations of Faith standalone page (minified)
│       ├── the_anatomy_of_worship.min.html  Anatomy of Worship standalone page (minified)
│       ├── the_weavers_plan.min.html    The Weaver's Plan 8-chapter curriculum (minified)
│       ├── prayerful_action.min.html    Prayerful Action standalone page (minified)
│       ├── the_call_to_forgive.min.html The Call to Forgive standalone page (minified)
│       ├── the_invitation.min.html      Invitation page (minified)
│       ├── the_commission.min.js        Deployment automation blueprint (minified)
│       ├── FlockOS_Blue.png             Logo (blue variant)
│       └── FlockOS_White.png            Logo (white variant)
│
├── index.html                         Public-facing site (auth-scoped navigation)
├── .gitignore
├── LICENSE                            Proprietary CRM + Free Learning Modules
└── README.md                          This file
```

## Quick Start

Full step-by-step instructions are in **the_pentecost.min.html** (in `Revelation/`) — open it in a browser for the best experience. The short version:

### 1. Create Google Sheets (4)
Create **Flock CRM**, **Flock Statistics**, **Flock Content**, and **Flock Missions** spreadsheets. Copy each Sheet ID.

### 2. Deploy FLOCK API (John)
1. Create a new Apps Script project at [script.google.com](https://script.google.com)
2. Add all 24 `.gs` files from `FlockOS-GS/` (everything except `Code.gs` needs a new Script file)
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

## Authentication

- **Method**: Email + passcode
- **Hashing**: SHA-256 with per-user salt + server-side pepper
- **Sessions**: 6-hour TTL, stored client-side
- **RBAC**: 6 levels — readonly (0), volunteer (1), care (2), leader (3), pastor (4), admin (5)

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
- **Control Panel (Admin Settings)** — Renamed from "Settings" to "Control Panel." All 8 sections are collapsible accordion panels (all closed by default) with summary counts/status in the trigger bar: Security, Identity & Branding, Display Preferences, Theme Administration, Visible Apps, System Settings, API Health, and Provisioning
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

## Minification & Build

FlockOS ships every JavaScript file in two forms:

| Form | Example | Purpose |
|------|---------|----------|
| **Source** | `the_tabernacle.js` | Human-readable, fully commented code for development and troubleshooting |
| **Minified** | `the_tabernacle.min.js` | Whitespace, comments, and redundant syntax stripped for production delivery |
| **HTML Source** | `the_gift_drift.html` | Readable standalone curriculum pages with Tailwind + Chart.js |
| **HTML Minified** | `the_gift_drift.min.html` | HTML minified via Rust-backed `minify_html` — tags, attributes, whitespace, inline JS/CSS all compressed |

HTML pages load the `.min.js` variants from `Acts/`. The unminified source files live in `Genesis/` (gitignored) so any developer can read, search, and debug the code in its original form locally.

### Why Minify?

- **~30% smaller payload** — the full Acts bundle drops from ~1.68 MB to ~1.17 MB, delivering an estimated 30% savings in page load time
- **Faster time-to-interactive** — smaller files parse and execute faster, especially on mobile devices and lower-bandwidth connections common in mission-field deployments
- **Consistent cross-platform loads** — reduced file sizes smooth out performance differences between desktop Chrome, mobile Safari (iOS), Android WebView, and other browsers, producing more uniform load times across platforms
- **No dependency risk** — minification is handled by `rjsmin` (JS) and `minify_html` (HTML) — two lightweight Python packages with no npm, Webpack, or framework toolchain required
- **Long-term reliability** — minified output is deterministic and reproducible. The build pipeline has zero network dependencies at build time, runs in under 2 seconds, and produces byte-identical output on any machine with the same Python venv. This means deployments are repeatable and the production bundle never drifts from what was tested

### Build Workflow

```bash
# From the FlockOS root:
./minify.sh
```

This activates the project's Python virtual environment (`.venv/`) and runs `FlockOS-GS/Exodus/minify.py`, which:

1. Reads every `.js` source file from `FlockOS-GS/Genesis/`
2. Writes each minified output as a `.min.js` file into `FlockOS-GS/Acts/`
3. Minifies the service worker (`the_living_water.js`) at the project root
4. Minifies standalone HTML pages in `FlockOS-GS/Revelation/` (`the_generations.html`, `the_anatomy_of_worship.html`, `the_gift_drift.html`) using `minify_html`
5. Prints a per-file size comparison and total reduction percentage

Run `./minify.sh` after any JavaScript or HTML code change to regenerate the production files.

### Development vs. Production

During development, you edit the readable source files in `Genesis/` (e.g., `the_tabernacle.js`). The code is written with clear variable names, section comments, and logical whitespace so problems are easy to trace. When you are ready to deploy, run the minifier — it reads from `Genesis/`, writes `.min.js` to `Acts/`, and the HTML pages already reference the `Acts/*.min.js` versions, so no further changes are needed. Only `Acts/` is committed to git; `Genesis/` and `Exodus/` are .gitignored.

## Music Stand Module

The frontend Music Stand (`FlockOS-GS/Genesis/the_shofar.js`) provides:
- Searchable song library with full CRUD
- Arrangement management with ChordPro chord notation
- Live Music Stand view — load a service plan, navigate songs with arrow keys
- PDF export — single arrangement or full setlist via jsPDF

Entry point: `window.openMusicStandApp()`

## Documentation

| File | Contents |
|------|----------|
| `the_pentecost.html` | Interactive deployment guide — open in browser |
| `2_Config.txt` | Module toggles, AppConfig defaults, action reference |
| `3_Deployment.txt` | 10-phase deployment guide with troubleshooting |
| `5_References.txt` | Complete schema — every tab, every column |
| `7_Vine.txt` | TheVine API client reference |
| `8_Wiring.txt` | Frontend-to-backend wiring audit |
| `9_Thematic_Approach.txt` | Scripture-rooted naming convention guide |

## License

**CRM Software** — Proprietary. Use, deployment, and modification require written permission from the author. Licensing available for churches and organizations upon request.

**Learning Modules** — Free for personal, church, and educational use.

See [LICENSE](LICENSE) for full terms.
