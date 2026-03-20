# ATOG(en) — "A Touch of the Gospel" Copilot Instruction Manual

## Project Identity

This is a full-stack Progressive Web App called "A Touch of the Gospel" (ATOG). It is a church management and discipleship platform built on Google Apps Script backends and a vanilla-JavaScript frontend. The developer and ministry leader is Greg Granger. The site is live, public-facing, and used by real church members. Treat every edit as production code.

---

## 1. Project Structure

**Root:**
- `index.html` — Production shell: loads `pages/AOSa.html` in an iframe
- `index-local.html` — Local dev shell: loads `pages/AOS1L.html` in an iframe

**pages/**
- `aos1p.html` — Production working file (develop & test here FIRST)
- `AOS1L.html` — Local preview mirror of aos1p
- `AOSa.html` — Stable production copy A (update only after release)
- `AOSb.html` — Stable production copy B (update only after release)
- `AOSc.html` — Stable production copy C (update only after release)
- `developer.html` — Developer contact card page
- `tbc_care.html` — Light-themed pastoral care page (unique sage/cream theme)
- `tap/dev.html` — TAP module subpage

**scripts/**
- `Scripts.js` — Script manifest & sequential loader (LOAD ORDER AUTHORITY)
- `config.js` — All endpoints, storage keys, auth config, app constants
- `Main.js` — Core routing, global utils, `init()`, DOMContentLoaded boot
- `Secure.js` — Vault session management, login/logout, session timer
- `Settings.js` — User settings, auth session writes, sign-out
- `MemberPortal.js` — Member self-service: profile, prayer submissions
- `Pastoral.js` — Pastoral dashboard: members CRUD, cards/table view
- `PrayerService.js` — Prayer request submission (API-first, no Google Forms)
- `PublicPrayer.js` — Public-facing prayer module
- `Todo.js` — 19-column todo management
- `Contact.js` — Contact card and communication links
- `Worship.js` — Worship/posture module
- `Posture.js` — Worship posture study
- `Focus.js` — Daily focus rotation (7 I-AM statements)
- `Explorer.js` — Bible book explorer
- `Bread.js` — Daily bread / devotional reading
- `Characters.js` — Biblical characters study
- `Words.js` — Word studies
- `Mirror.js` — Pastoral mirror diagnostic
- `Heart.js` — Heart condition diagnostic
- `BibleQuiz.js` — Bible knowledge quiz
- `Counseling.js` — Biblical counseling content
- `Apologetics.js` — Apologetics module
- `Family.js` — Family/household data
- `Missions.js` — World missions & persecution data (48 country dossiers)
- `Statistics.js` — Church statistics & analytics
- `Analysis.js` — Data analysis views
- `AdminProvision.js` — Admin provisioning tools
- `Invitation.js` — Gospel invitation content (loaded last, in footer)
- `Psalms.js` — Psalms study module
- `Theology.js` — Theology reference module
- `Outreach.js` — Outreach & directions
- `Disclaimer.js` — Legal disclaimers

**backend/expansion/** — FLOCK OS: next-gen multi-church backend (Google Apps Script)
- `api.gs` — Central doGet/doPost router, action dispatcher
- `auth.gs` — Self-contained auth: salt-pepper-hash, RBAC, sessions
- `setup.gs` — Schema setup: creates all 87 tabs with column headers
- `database.gs` — Core CRUD helpers, sheet access patterns
- `utilities.gs` — `generateId`, `toBool`, `isoDate`, `isoNow`, `escapeHtml`, etc.
- `todo.gs` — Todo CRUD handler
- `communications.gs` — Communications hub
- `compassion.gs` — Compassion ministry
- `discipleship.gs` — Discipleship tracking
- `learning.gs` — Learning resources
- `member-cards.gs` — Member card generation
- `ministries.gs` — Ministry management
- `outreach.gs` — Outreach operations
- `photos.gs` — Photo management
- `sermons.gs` — Sermon library
- `service-planning.gs` — Service planning
- `spiritual-care.gs` — Spiritual care tracking
- `statistics.gs` — Statistics module
- `theology.gs` — Theology backend
- `world-missions.gs` — Missions data
- `ExtraAPI-setup.gs` — 53-tab EXTRA API schema setup

**backend/expansion/ numbered docs:**
- `0-FlockOS-ExpansionPlanning.txt` — Sheet IDs, links, architectural decisions
- `1-FlockOS-Deployment.txt` — Full deployment guide
- `2-FlockOS-Architecture.txt` — 4-API architecture & RBAC reference
- `3-FlockOS-SaltPepperHash.gs` — Security: hashing module reference
- `4-FlockOS-Expansions.gs` — Multi-church, batch, scheduling plans
- `5-FlockOS-ConfigInfo.txt` — Configuration reference
- `6-FlockOS-AdornmentCSS.txt` — 8-theme design system (Adornment CSS)
- `7-FlockOS-References.txt` — All 197 tabs, every column header
- `8-FlockOS-TheVine.txt` — The Vine API client reference

**backend/pastoral-server-v2/** — Legacy pastoral backend (still active)
- `code.gs`, `contacts.gs`, `members.gs`, `Pastoral.gs`, `prayer.gs`, `setup.gs`, `todo.gs`
- `DeployedAuth.gs` — Auth verification
- `vision.txt` — Pastoral vision doc

---

## 2. Four-API Architecture (The Four Gospels)

The backend consists of 4 independent Google Apps Script Web Apps, each backed by its own Google Spreadsheet. They are named after the four Gospels:

### Matthew (APP API) — Public content, read-only, NO auth required
- Query: `?tab=TabName` (returns JSON array of rows)
- Tabs: Books, Genealogy, Counseling, Devotionals, Reading, Words, Heart, Mirror, Theology, Quiz, Apologetics, Config, Psalms, Family
- 3-endpoint failover: `APP_ENDPOINTS[0..2]` in `config.js`

### John (FLOCK API) — Church management, auth + RBAC required
- Query: `?action=namespace.verb&token=TOKEN&email=EMAIL&...params`
- Response: `{ ok: true/false, data/rows/row: ..., message: "..." }`
- Namespaces: `auth.*`, `users.*`, `members.*`, `prayer.*`, `todo.*`, `attendance.*`, `events.*`, `smallgroups.*`, `giving.*`, `volunteers.*`, `communications.*`, `ministries.*`, `serviceplans.*`, `spiritualcare.*`, `outreach.*`, `photos.*`, `sermons.*`, `compassion.*`, `discipleship.*`, `learning.*`, `theology.*`, `membercards.*`, `statistics.*`, `multichurch.*`
- 76 tabs across pastoral core, content, and expansion modules

### Mark (MISSIONS API) — Global missions & persecution data
- 56 tabs: 8 structured + 48 country dossiers
- Persecution scoring: Open Doors model (5 pressure spheres + violence)

### Luke (EXTRA API) — Statistics, analytics, 50 future-expansion slots
- 53 tabs: 3 statistics + 50 generic (Extra_01..Extra_50, 50 cols each)

---

## 3. The Vine — Centralized API Client

The Vine is a client-side API abstraction with four "branches" matching the four Gospel APIs. It is inlined in the HTML pages, not a separate `.js` file.

**Usage pattern:**

```javascript
TheVine.app.devotionals()                          // Matthew: public data
TheVine.flock.auth.login({ email, passcode })       // John: auth
TheVine.flock.members.list({ status: 'active' })    // John: members
TheVine.flock.prayer.create({ prayerText, ... })    // John: prayer
TheVine.missions.countries.get({ country: 'Iran' }) // Mark: missions
TheVine.extra.statistics.dashboard()                // Luke: stats
```

Branch aliases: `matthew=app`, `john=flock`, `mark=missions`, `luke=extra`

**Internal fetch engine:**
- Auto-injects auth token + email from session if available
- Appends action param + cache-bust timestamp (`_=Date.now()`)
- 12-second `AbortController` timeout
- Builds URL as: `baseUrl + '?' + URLSearchParams(params).toString()`

**Session management:**
- Reads from `sessionStorage` key `flock_auth_session`
- Token fields (fallback chain): `token > accessToken > authToken > jwt`
- 6-hour TTL (`expiresAt = Date.now() + 6h`)
- Auto-clears expired sessions

---

## 4. Authentication & Security

**Auth model:** Email + passcode (supports plain or SHA-256 hashed)

### Salt-Pepper-Hash (3-layer)
- **Layer 1 — Pepper:** server-wide secret in Google Script Properties  
  Key: `FLOCK_AUTH_PEPPER` (32-char hex, generated by `initPepper()`)
- **Layer 2 — Salt:** per-user random in AuthUsers column D  
  Generated by `generateSalt_()` (32-char hex)
- **Layer 3 — Hash:** `SHA-256(pepper + salt + passcode)`  
  Stored in AuthUsers column C as 64-char lowercase hex

**Login flow:**
1. Client sends `?action=auth.login&email=X&passcode=Y`
2. Backend hashes passcode with user's salt + server pepper
3. Compares against stored hash (with 7 legacy fallback formats)
4. Returns `{ ok, token, email, role, expiresAt }`
5. Frontend stores in `sessionStorage`

### RBAC (5 levels)
```
readonly (0) < volunteer (1) < leader (2) < pastor (3) < admin (4)
```
- `requireRole(auth, minLevel)` enforces on every protected route
- `filterPrayerForRole()` / `filterMemberForRole()` apply field-level filtering

**Frontend role allowlist:**  
`AUTH_ROLE_ALLOWLIST = ['Member', 'Leader', 'Admin', 'Pastor', 'Deacon']`

### Session Architecture
- **Secure Vault:** `sessionStorage` key `atogen_secure_vault_v1`  
  1-year idle timeout, touched every 5 seconds on activity  
  15-second expiry timer checks session freshness
- **Settings Auth:** `localStorage` key `aos_auth_session_v1`  
  Stores `{ token, email, role, at: Date.now() }`
- **Profile Cache:** `localStorage` key `aos_auth_profile_v1`  
  6-hour TTL (`AUTH_PROFILE_CACHE_TTL_MS`)

**Token verification on backend:**  
`TOKEN_CACHE_TTL_SECONDS = 300` (5 minutes) — then re-verifies against auth

**NEVER** store plaintext passwords. **NEVER** log tokens. **NEVER** expose pepper/salt in client code. Always validate and sanitize user input. Use `escapeHtml()` for all rendered text.

---

## 5. Frontend Patterns & Conventions

### Script Loading
`Scripts.js` is the **SINGLE SOURCE OF TRUTH** for load order. `AOS1P_HEAD_SCRIPTS` defines the ordered manifest. Scripts are loaded sequentially via `loadScriptsSequentially()`. CDN deps load first:
1. Lucide icons (unpkg)
2. Tailwind CSS (CDN)
3. Chart.js (CDN)
4. `config.js` → `Main.js` → feature modules → `Invitation.js` (last)

### Boot Sequence
1. Shell (`index.html`) loads iframe → `aos1p.html`
2. `aos1p.html` loads `Scripts.js` which chains all module scripts
3. When all scripts loaded: dispatches `'aos:scripts-ready'` event
4. `Main.js`: DOMContentLoaded → `runInitOnce()` → `init()`
5. `init()` installs anchor routing, rebuilds app links, starts clock, applies saved theme/bg, kicks off sitewide data sync

### Naming Conventions
- **Functions:** camelCase, prefixed by module or verb  
  `openExplorer()`, `pastoralFetchData()`, `mpFetchProfile()`  
  `handleMembersList()`, `todoNormalizeRow()`
- **State:** `moduleNameAppState` object per module  
  `pastoralAppState`, `mirrorAppState`, `todoAppState`
- **Constants:** `UPPER_SNAKE_CASE`  
  `MASTER_API_URL`, `AUTH_SESSION_KEY`, `API_RESPONSE_CACHE_TTL_MS`
- **Globals:** `window.PROPERTY_NAME` for cross-module access
- **Var choice:** `MemberPortal.js` uses `var` (with `_mp` prefix to avoid collisions). Most other modules use `const`/`let`

### Module Pattern
Each script file is one self-contained module. No `import`/`export` (vanilla JS). Each module exposes a main launcher function: `openFocusApp()`, `openExplorer()`, `openPastoralApp()`, `openMirrorApp()`, `openHeartApp()`, `openQuizApp()`, `openStatisticsApp()`, `openContactApp()`, etc.

**Launcher function pattern:**
1. Set modal title/subtitle/back text via `getElementById`
2. Fetch data from API (`MASTER_API_URL?tab=TabName` or `action=namespace.verb`)
3. Normalize and cache response in module state
4. Render UI into `modal-body-container` via `innerHTML` template literals
5. Open modal: `document.getElementById('data-modal').classList.add('active')`

### DOM Manipulation
- `getElementById()` for element access (never `querySelector` for IDs)
- `innerHTML` with template literals for rich content rendering
- Inline styles in template literals (CSS-in-JS pattern)
- `classList.add/remove/toggle` for state classes
- Mix of `onclick=""` attributes and `addEventListener` for events

### Modal System
Single global modal element in `aos1p.html`:

```html
<div id="data-modal" class="data-modal">
  <div class="modal-body" id="modal-body-container"></div>
</div>
```

- **Open:** `document.getElementById('data-modal').classList.add('active')`
- **Close:** `closeModal()` — removes `'active'` class
- **Title:** `document.getElementById('modal-title').innerText = '...'`
- **Body:** `document.getElementById('modal-body-container').innerHTML = htmlContent`

Overlay modals (Pastoral): `pa-member-overlay`, `pa-notes-overlay`
- Backdrop click or X button to close
- Status text colors: `#22d3ee` (success), `#f87171` (error), `#94a3b8` (neutral)
- Disabled/enabled submit during saves

### State Management
No Redux/Vuex. Each module has a local state object:

```javascript
const pastoralAppState = {
  rows: [], loaded: false, loading: false, filter: '',
  editorMode: 'create', showArchived: false, viewMode: 'table'
};
```

Persist to `localStorage` when needed. TTL-based cache timestamps.

### API Call Pattern

```javascript
const auth = getPastoralAuthPayload();  // or session read
const params = new URLSearchParams({
  action: 'namespace.verb',
  token: auth.token,
  email: auth.email,
  ...additionalParams,
  _: String(Date.now())               // cache-bust
});
const res = await fetch(endpoint + '?' + params.toString(), { cache: 'no-store' });
const data = await res.json();
if (!data.ok) throw new Error(data.message);
```

Public content (APP API):
```javascript
fetch(MASTER_API_URL + '?tab=Books&_=' + Date.now())
```

### Error Handling
- Try-catch with user-facing messages in modals or status text
- Some modules fall back to cached data on fetch failure
- Console warnings (`console.warn`) for non-critical failures
- Never let uncaught errors break the UI

### Anchor Routing
Hash/path-based routing via `Main.js`:
- `MAIN_SCREEN_APP_LINKS` maps element IDs to paths:  
  `'app-focus' → '/focus'`, `'app-missions' → '/missions'`, etc.
- `popstate` listener handles back/forward navigation
- `openAppFromAnchor(path)` opens the matching module
- `normalizeAnchorPath()` cleans paths for comparison

---

## 6. Design System (Adornment CSS — 8 Themes)

### Light Themes (4)
- **Dayspring** (default) — Warm ivory (`#faf9f6`), soft sky blue (`#7eaacc`)
- **Meadow** — Sage green (`#6ba88a`), botanical cream (`#f6f7f4`)
- **Lavender** — Soft violet (`#9b7ec8`), dove grey (`#f8f6fb`)
- **Rosewood** — Blush pink (`#c27d8f`), warm cream (`#fbf6f7`)

### Dark Themes (4)
- **Vesper** (dark default) — Deep navy (`#0f1118`), soft pastels
- **Evergreen** — Forest dark (`#0e1510`), sage accent (`#6ec496`)
- **Twilight** — Deep purple (`#12101a`), violet pastels (`#a088d0`)
- **Obsidian** — True black (`#000000`), OLED-safe high contrast

Auto theme: respects `prefers-color-scheme` (Dayspring light / Vesper dark)

### Semantic Color Variables (per theme)
`--accent`, `--mint`, `--peach`, `--lilac`, `--rose`, `--gold`, `--sky`, `--danger`, `--success`, `--warning`, `--bg-deep`, `--panel-bg`, `--text-main`

### Current Dark Palette (Vesper/default)
- `--bg-deep: #020617`
- `--accent-cyan: #38bdf8`
- `--accent-green: #2dd4bf`
- `--accent-gold: #c084fc`
- `--accent-magenta: #f472b6`
- Panel borders: `rgba(255,255,255,0.1)` to `rgba(255,255,255,0.3)`
- Panel bg: `rgba(15,23,42,0.65)` for inputs
- Overlay bg: `rgba(2,6,23,0.4)`

### Font Stack
- Serif (headlines, quotes): Merriweather 300/400/700
- Sans-serif (body, UI): Inter 300–600
- Monospace (labels, code): Fira Code 400, fallback JetBrains Mono

### Component Classes (from Adornment CSS)
- `.card` — Rounded panel (18–22px border-radius), semi-transparent
- `.pill` — Rounded badge with color variants (`.pill-accent`, `.pill-mint`...)
- `.btn` — Primary/secondary/ghost/danger button styles
- `.input`, `.textarea`, `.select` — Form elements with focus ring
- `.avatar` — Circular initials (sm/md/lg/xl)
- `.status-dot` — Inline presence indicator
- `.grid-2/3/4` — Responsive grid utilities
- `.skeleton` — Loading shimmer animation
- `.toast` — Notification snackbar (toggle via `.show` class)

### Responsive Breakpoints
- Mobile-first: `max-width 768px` triggers single-column, sidebar overlay
- Touch targets: 0.5–1.25rem padding on pills/buttons
- Font scaling: `clamp()` for responsive sizing
- Theme transition: `background 0.35s ease`, `color 0.25s ease`

### Status Bar
- 50px height, Fira Code monospace, cyan accent text
- Cellular-style traffic bars (1–5 bars based on API request frequency)
- Connection dot (online/offline indicator)
- Clock display (click = factory reset of session + hard reload)

### App Grid
- Responsive grid of 100px app cards, 22px border-radius
- Semi-transparent panels on dark gradient background
- Icon + label layout per card

### Inline Style Pattern (CSS-in-JS)
Template literals with inline styles referencing CSS variables:

```javascript
`<div style="
  padding: 20px;
  background: var(--panel-bg);
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 18px;
  color: var(--text-main);
">${content}</div>`
```

Specific values frequently used:
- Font sizes: `.66–.95rem` (labels/content), `.82rem` (buttons)
- Letter-spacing: `.06–.1em` (labels)
- Border-radius: 14–24px (cards/modals), 6–8px (pills/inputs)
- Accent colors: `#38bdf8`, `#22d3ee`, `#2dd4bf`, `#c084fc`, `#f472b6`

---

## 7. Backend Code Patterns (Google Apps Script)

### Routing (api.gs)

```javascript
function doGet(e)  { return expansionDoGet(e); }
function doPost(e) { return expansionDoPost(e); }
```

Central dispatcher routes `?action=namespace.verb` to handlers. All responses wrapped by `asJson()` → `ContentService.createTextOutput()`

### Handler Pattern

```javascript
function handleMembersCreate(params, auth) {
  requireRole(auth, 'leader');
  var row = [generateId(), params.firstName, params.lastName, ...];
  var sheet = getTab('Members');
  sheet.appendRow(row);
  writeAudit(auth, 'members.create', 'Members', lastRow, 'Created member');
  return jsonOk({ message: 'Created.', id: row[0] });
}
```

### Response Helpers
- `jsonOk(data)` → `{ ok: true, ...data }`
- `jsonErr(message)` → `{ ok: false, message }`
- `asJson(payload)` → `ContentService.createTextOutput(JSON.stringify(payload))`

### Naming
- **Handlers:** `handleXxxCreate()`, `handleXxxList()`, `handleXxxGet()`, `handleXxxUpdate()`
- **Tab names:** `'Members'`, `'PrayerRequests'`, `'AuthUsers_v1'`, etc.
- **Column maps:** `var MEMBER = { ID: 0, FIRST_NAME: 1, LAST_NAME: 2, ... }`
- **Converters:** `memberRowToObject(vals, rowIndex)`, `prayerRowToObject(vals, i)`

### Sheet Access

```javascript
var sheet = getTab('TabName');
var data = sheet.getRange(2, 1, sheet.getLastRow() - 1, colCount).getValues();
// Row 1 is always headers; data starts at row 2
```

### Audit Trail
```javascript
writeAudit(auth, 'namespace.verb', 'SheetTab', rowIndex, details)
// Logged to Audit tab with timestamp, user email, action, target
```

### Utilities (utilities.gs)
- `generateId()` — 12-char UUID
- `toBool(val)` — `String(val).toUpperCase() === 'TRUE'`
- `isoDate(cellValue)` — Convert to ISO string
- `isoNow()` — Current ISO timestamp
- `normalizeEmail(val)` — `toLowerCase().trim()`
- `escapeHtml(str)` — Sanitize for output
- `getTab(tabName)` — Fetch sheet by name
- `getConfig(key, fb)` — Read from `PropertiesService`

---

## 8. Endpoints & Configuration Reference

### APP API (Matthew) — 3-endpoint failover
- `APP_ENDPOINTS[0]` = PRIMARY (public content)
- `APP_ENDPOINTS[1]` = SECONDARY (failover)
- `APP_ENDPOINTS[2]` = TERTIARY (failover)
- `MASTER_API_URL = APP_ENDPOINTS[0]`

### AUTH API (John — FLOCK)
- `AUTH_PRIMARY_ENDPOINT` (login, profile, refresh, logout)

### Pastoral DB v2 (Legacy)
- `PASTORAL_DB_V2_ENDPOINT` (members, prayer, contacts)

### Prayer
- `PRAYER_REQUEST_ENDPOINT` (falls back to `localhost:3000` in dev)
- Prayer is API-first (no Google Forms)

### Pastoral Notes
- `PASTORAL_NOTES_ENDPOINT`

### Todo
- `TODO_ENDPOINT = PASTORAL_NOTES_ENDPOINT` (shares same sheet)

### Failover
- `resolveWorkingEndpoint()` pings all `APP_ENDPOINTS`, returns first that responds
- `API_FAILOVER_DELAY_MS = 1500`
- `API_PRIMARY_CHECK_INTERVAL_MS = 30000`
- Automatic overlay when all endpoints fail

### Caching
- `API_RESPONSE_CACHE_TTL_MS` = 20 minutes
- `API_SITEWIDE_SYNC_INTERVAL_MS` = 3 minutes
- `API_SITEWIDE_SYNC_CONCURRENCY` = 4 parallel fetches
- Sitewide sync preloads: Devotionals, Reading, Words, Counseling, Books, Genealogy, Theology, Mirror, Heart, Quiz, Psalms, Family, Apologetics

### Storage Keys
- `APP_VISIBILITY_KEY = 'app_visibility'`
- `AUTH_SESSION_KEY = 'aos_auth_session_v1'`
- `AUTH_PROFILE_KEY = 'aos_auth_profile_v1'`
- `SAUCE_PROFILE_STORAGE_PREFIX = 'sauce_profile_v1'`
- Secure vault key = `'atogen_secure_vault_v1'`

---

## 9. Workflow Rules

### Development
- All work happens in `aos1p.html` first (production working file)
- `AOS1L.html` is the local dev preview (mirrors aos1p)
- **Do NOT update `AOSa.html`, `AOSb.html`, or `AOSc.html` without explicit user approval for a release.** These are stable production copies.
- Test in `aos1p.html` → get confirmation → then sync to stable copies

### Editing
- Read any file you plan to modify BEFORE editing it
- Preserve existing naming conventions within each module
- Respect the script load order in `Scripts.js`
- Never break cross-module `window.GLOBAL` references
- Keep modules self-contained — no `import`/`export`

### Code Style
- Vanilla JavaScript only (no frameworks, no bundlers, no TypeScript)
- Template literals with inline styles for UI rendering
- Reference CSS variables (`--accent`, `--panel-bg`, etc.) in inline styles
- Use Merriweather for serif text, Inter for sans-serif, Fira Code for mono
- Dark-first design: always test in dark theme (Vesper) and light (Dayspring)
- Keep border-radius between 14–24px for cards, 6–8px for small elements
- Use `rgba()` for borders and backgrounds with appropriate transparency
- Status text: `#22d3ee` success, `#f87171` error, `#94a3b8` neutral/muted

### Backend Edits
- Google Apps Script (`.gs` files): ES5-compatible JavaScript (no `let`/`const`, no arrow functions, no template literals, no `async`/`await`)
- Use `var` for all declarations in `.gs` files
- Every handler must call `requireRole()` unless it is a public route
- Always call `writeAudit()` for mutating operations
- Always sanitize text output with `escapeHtml()`
- Use `getTab()` for sheet access, never hardcode sheet references
- Row data is 0-indexed arrays; row 1 is always headers

### Security
- Never expose API endpoints as clickable links or in user-facing text
- Never log or display tokens, passwords, salts, or pepper values
- Always validate auth before any protected operation
- Use the salt-pepper-hash scheme for any new password storage
- Sanitize all user input before rendering (`escapeHtml` on frontend, sanitize on backend)
- Session tokens have 6-hour TTL; respect expiry checks

---

## 10. Data Architecture Quick Reference

### FLOCK API (John) — 76 tabs
- **Pastoral Core (7):** Members (51 cols), PrayerRequests (17 cols), ContactLog, PastoralNotes, Milestones, Households, ToDo (19 cols)
- **Content (8):** Books, Genealogy, Counseling, Devotionals, Reading, Words, Heart, Mirror
- **Auth (3):** AuthUsers_v1, UserProfiles_v1, AuthAudit_v1
- **Expansion:** Attendance, Events, SmallGroups, Giving, Volunteers, Communications (legacy + Hub), Ministries, ServicePlans, SpiritualCare, Outreach, Photos, Sermons, Compassion, Discipleship, Learning, Theology, MemberCards, Statistics, MultiChurch, AccessControl

### Missions API (Mark) — 56 tabs
- **Structured (8):** MissionsRegistry, MissionsRegions, MissionsCities, MissionsPartners, MissionsPrayerFocus, MissionsUpdates, MissionsTeams, MissionsMetrics
- **Country Dossiers (48):** One tab per country with persecution scores

### Extra API (Luke) — 53 tabs
- StatisticsConfig, StatisticsSnapshots, StatisticsCustomViews
- Extra_01 through Extra_50 (50 generic columns c01–c50 each)

### APP API (Matthew) — 14 tabs
Books, Genealogy, Counseling, Devotionals, Reading, Words, Heart, Mirror, Theology, Quiz, Apologetics, Config, Psalms, Family

### Prayer Data (17 cols)
ID, MemberId, Name, Email, Phone, Text, Category, Confidential, FollowUp, Status, AdminNotes, AssignedTo, SubmittedAt, LastUpdated, UpdatedBy, Archived, AutoLog

**Categories:** Health, Family, Financial, Spiritual Growth, Grief/Loss, Relationships, Work/Career, Praise Report, Other

---

## 11. Common Data Objects

- `invitationData(3)` — Gospel invitation entries
- `iamData(7)` — 7 I-AM statements for daily focus
- `workData(4)` — Work-related content entries
- `missionList(48 countries)` — World missions data
- `quizLinks(2)` — Bible quiz link entries
- `diagnosticToolsLinks(3)` — Diagnostic tool links
- `researchLinks(3)` — Research resource links
- `explorerData(API)` — Bible book data from APP API
- `MAIN_SCREEN_APP_LINKS` — Maps element IDs → anchor paths for all modules

---

## 12. Known Technical Considerations

**SESSION STABILITY:** Backend token cache 5min (`TOKEN_CACHE_TTL_SECONDS=300`). PWA suspend/resume can cause re-validation issues. Session touch throttle 5s. 15-second expiry timer checks `getFreshSecureSessionPayload()` — if not fresh, `showLogin()` clears session.

**CLOCK TAP:** Clicking status bar clock triggers factory reset (clears vault + hard reload). Intentional user-triggered behavior.

**API FAILOVER:** Primary → secondary → tertiary, 5s timeout, 3 retries. Loading overlay appears during failover. Traffic bars reflect API frequency.

**LEGACY:** `verifyPasscode()` supports 7 fallback hash formats for migration. `MemberPortal.js` uses `var`/`_mp` prefix. Lucide gets no-op fallback if CDN fails.

**CACHE BUSTING:** All API calls append `_=Date.now()`. `Scripts.js` appends `v=` and `_t=` tokens. `index.html` uses `shellVersion` + runtime token for iframe busting. Meta tags: `no-cache, no-store, must-revalidate`.

---

## 13. Content & Ministry Context

Church app for "A Touch of the Gospel" ministry led by Greg Granger. Focus: discipleship, biblical counseling, world missions (persecuted church), pastoral care, prayer, worship, Bible study tools, community connection. Serves public visitors (gospel, prayer, Bible content) and authenticated members (portal, pastoral dashboard, todos, stats, admin). Tone: biblically grounded, pastorally warm, theologically precise.

---

## 14. AI Assistant Rules Summary

### DO
- Read files before editing them
- Develop and test in `aos1p.html` first
- Follow existing naming conventions per module
- Use vanilla JavaScript (no frameworks)
- Use template literals with inline styles for UI
- Reference CSS variables for theming
- Sanitize all user input (`escapeHtml`)
- Validate auth before protected operations
- Write audit trails for mutations
- Use ES5 in `.gs` files (`var`, `function`, no arrows)
- Keep modules self-contained
- Test in both dark (Vesper) and light (Dayspring) themes

### DO NOT
- Update `AOSa`/`AOSb`/`AOSc` without explicit release approval
- Add npm/bundler/framework dependencies
- Use `import`/`export` syntax in frontend scripts
- Use `let`/`const`/arrow functions/template literals in `.gs` files
- Expose tokens, passwords, or API keys in client-facing output
- Break the script load order defined in `Scripts.js`
- Skip auth checks on protected routes
- Hardcode sheet/tab references (use `getTab()`)
- Render unsanitized user text as HTML
- Guess at code structure — read it first
