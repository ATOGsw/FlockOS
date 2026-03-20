# FlockOS — "A Touch of the Gospel" (ATOG)

A full-stack Progressive Web App for church management and discipleship, built on Google Apps Script backends and a vanilla-JavaScript frontend.

**Developer & Ministry Leader:** Greg Granger  
**Ministry:** A Touch of the Gospel (ATOG)  
**Status:** Live, public-facing, used by real church members

---

## Project Overview

ATOG is a church management and discipleship platform consisting of:

- **Frontend PWA** — Vanilla JavaScript, loaded via an HTML shell with an iframe architecture
- **Four Gospel APIs** — Four independent Google Apps Script Web Apps (Matthew, Mark, Luke, John) backed by Google Spreadsheets
- **Adornment CSS** — 8-theme design system (4 light, 4 dark)
- **The Vine** — Centralized client-side API abstraction layer

---

## Project Structure

```
FlockOS-/
├── index.html              # Production shell (loads pages/AOSa.html in iframe)
├── index-local.html        # Local dev shell (loads pages/AOS1L.html in iframe)
│
├── pages/
│   ├── aos1p.html          # Production working file — develop & test here FIRST
│   ├── AOS1L.html          # Local preview mirror of aos1p
│   ├── AOSa.html           # Stable production copy A (update only after release)
│   ├── AOSb.html           # Stable production copy B (update only after release)
│   ├── AOSc.html           # Stable production copy C (update only after release)
│   ├── developer.html      # Developer contact card page
│   ├── tbc_care.html       # Light-themed pastoral care page (sage/cream theme)
│   └── tap/dev.html        # TAP module subpage
│
├── scripts/
│   ├── Scripts.js          # Script manifest & sequential loader (LOAD ORDER AUTHORITY)
│   ├── config.js           # All endpoints, storage keys, auth config, app constants
│   ├── Main.js             # Core routing, global utils, init(), DOMContentLoaded boot
│   ├── Secure.js           # Vault session management, login/logout, session timer
│   ├── Settings.js         # User settings, auth session writes, sign-out
│   ├── MemberPortal.js     # Member self-service: profile, prayer submissions
│   ├── Pastoral.js         # Pastoral dashboard: members CRUD, cards/table view
│   ├── PrayerService.js    # Prayer request submission (API-first, no Google Forms)
│   ├── PublicPrayer.js     # Public-facing prayer module
│   ├── Todo.js             # 19-column todo management
│   ├── Contact.js          # Contact card and communication links
│   ├── Worship.js          # Worship/posture module
│   ├── Posture.js          # Worship posture study
│   ├── Focus.js            # Daily focus rotation (7 I-AM statements)
│   ├── Explorer.js         # Bible book explorer
│   ├── Bread.js            # Daily bread / devotional reading
│   ├── Characters.js       # Biblical characters study
│   ├── Words.js            # Word studies
│   ├── Mirror.js           # Pastoral mirror diagnostic
│   ├── Heart.js            # Heart condition diagnostic
│   ├── BibleQuiz.js        # Bible knowledge quiz
│   ├── Counseling.js       # Biblical counseling content
│   ├── Apologetics.js      # Apologetics module
│   ├── Family.js           # Family/household data
│   ├── Missions.js         # World missions & persecution data (48 country dossiers)
│   ├── Statistics.js       # Church statistics & analytics
│   ├── Analysis.js         # Data analysis views
│   ├── AdminProvision.js   # Admin provisioning tools
│   ├── Invitation.js       # Gospel invitation content (loaded last, in footer)
│   ├── Psalms.js           # Psalms study module
│   ├── Theology.js         # Theology reference module
│   ├── Outreach.js         # Outreach & directions
│   └── Disclaimer.js       # Legal disclaimers
│
└── backend/
    ├── expansion/          # FLOCK OS: next-gen multi-church backend (Google Apps Script)
    │   ├── api.gs              # Central doGet/doPost router, action dispatcher
    │   ├── auth.gs             # Self-contained auth: salt-pepper-hash, RBAC, sessions
    │   ├── setup.gs            # Schema setup: creates all 87 tabs with column headers
    │   ├── database.gs         # Core CRUD helpers, sheet access patterns
    │   ├── utilities.gs        # generateId, toBool, isoDate, isoNow, escapeHtml, etc.
    │   ├── todo.gs             # Todo CRUD handler
    │   ├── communications.gs   # Communications hub
    │   ├── compassion.gs       # Compassion ministry
    │   ├── discipleship.gs     # Discipleship tracking
    │   ├── learning.gs         # Learning resources
    │   ├── member-cards.gs     # Member card generation
    │   ├── ministries.gs       # Ministry management
    │   ├── outreach.gs         # Outreach operations
    │   ├── photos.gs           # Photo management
    │   ├── sermons.gs          # Sermon library
    │   ├── service-planning.gs # Service planning
    │   ├── spiritual-care.gs   # Spiritual care tracking
    │   ├── statistics.gs       # Statistics module
    │   ├── theology.gs         # Theology backend
    │   ├── world-missions.gs   # Missions data
    │   ├── ExtraAPI-setup.gs   # 53-tab EXTRA API schema setup
    │   ├── 0-FlockOS-ExpansionPlanning.txt  # Sheet IDs, links, architectural decisions
    │   ├── 1-FlockOS-Deployment.txt         # Full deployment guide
    │   ├── 2-FlockOS-Architecture.txt       # 4-API architecture & RBAC reference
    │   ├── 3-FlockOS-SaltPepperHash.gs      # Security: hashing module reference
    │   ├── 4-FlockOS-Expansions.gs          # Multi-church, batch, scheduling plans
    │   ├── 5-FlockOS-ConfigInfo.txt         # Configuration reference
    │   ├── 6-FlockOS-AdornmentCSS.txt       # 8-theme design system (Adornment CSS)
    │   ├── 7-FlockOS-References.txt         # All 197 tabs, every column header
    │   └── 8-FlockOS-TheVine.txt            # The Vine API client reference
    │
    └── pastoral-server-v2/     # Legacy pastoral backend (still active)
        ├── code.gs
        ├── contacts.gs
        ├── members.gs
        ├── Pastoral.gs
        ├── prayer.gs
        ├── setup.gs
        ├── todo.gs
        ├── DeployedAuth.gs     # Auth verification
        └── vision.txt          # Pastoral vision doc
```

---

## Four-API Architecture ("The Four Gospels")

| API | Gospel | Purpose | Auth |
|-----|--------|---------|------|
| APP API | Matthew | Public content, read-only | None |
| FLOCK API | John | Church management, CRUD | RBAC required |
| MISSIONS API | Mark | Global missions & persecution data | None |
| EXTRA API | Luke | Statistics, analytics, expansion slots | Auth optional |

### Matthew (APP API)
- Public content, read-only — no auth required
- Query: `?tab=TabName` → returns JSON array of rows
- 3-endpoint failover: `APP_ENDPOINTS[0..2]` in `config.js`
- Tabs: Books, Genealogy, Counseling, Devotionals, Reading, Words, Heart, Mirror, Theology, Quiz, Apologetics, Config, Psalms, Family

### John (FLOCK API)
- Church management — auth + RBAC required
- Query: `?action=namespace.verb&token=TOKEN&email=EMAIL&...params`
- Response: `{ ok: true/false, data/rows/row: ..., message: "..." }`
- 76 tabs across pastoral core, content, and expansion modules

### Mark (MISSIONS API)
- Global missions & persecution data
- 56 tabs: 8 structured + 48 country dossiers
- Persecution scoring: Open Doors model (5 pressure spheres + violence)

### Luke (EXTRA API)
- Statistics, analytics, future-expansion slots
- 53 tabs: 3 statistics + 50 generic (Extra_01..Extra_50, 50 cols each)

---

## The Vine — API Client

The Vine is a client-side API abstraction inlined in the HTML pages.

```javascript
TheVine.app.devotionals()                          // Matthew: public data
TheVine.flock.auth.login({ email, passcode })       // John: auth
TheVine.flock.members.list({ status: 'active' })    // John: members
TheVine.flock.prayer.create({ prayerText, ... })    // John: prayer
TheVine.missions.countries.get({ country: 'Iran' }) // Mark: missions
TheVine.extra.statistics.dashboard()                // Luke: stats
```

Branch aliases: `matthew=app`, `john=flock`, `mark=missions`, `luke=extra`

---

## Authentication & Security

- **Auth model:** Email + passcode (supports plain or SHA-256 hashed)
- **Salt-Pepper-Hash:** 3-layer security (server pepper + per-user salt + SHA-256 hash)
- **RBAC:** 5 levels — `readonly(0) < volunteer(1) < leader(2) < pastor(3) < admin(4)`
- **Session TTL:** 6 hours; stored in `sessionStorage`

Security rules:
- Never store plaintext passwords
- Never log tokens
- Never expose pepper/salt in client code
- Always validate and sanitize user input
- Use `escapeHtml()` for all rendered text

---

## Design System — Adornment CSS (8 Themes)

**Light Themes:** Dayspring (default), Meadow, Lavender, Rosewood  
**Dark Themes:** Vesper (default), Evergreen, Twilight, Obsidian

**Font Stack:**
- Serif (headlines/quotes): Merriweather 300/400/700
- Sans-serif (body/UI): Inter 300–600
- Monospace (labels/code): Fira Code 400

---

## Development Workflow

1. **All work starts in `pages/aos1p.html`** — the production working file
2. Mirror changes to `pages/AOS1L.html` for local preview
3. **Do NOT update `AOSa.html`, `AOSb.html`, or `AOSc.html` without explicit release approval**
4. Test in both dark (Vesper) and light (Dayspring) themes

### Script Load Order
`Scripts.js` is the **single source of truth** for script load order.  
CDN deps load first (Lucide, Tailwind, Chart.js), then:  
`config.js` → `Main.js` → feature modules → `Invitation.js` (last)

### Backend (Google Apps Script)
- Use **ES5-compatible JavaScript** only — no `let/const`, no arrow functions, no template literals, no `async/await`
- Use `var` for all declarations in `.gs` files
- Every handler must call `requireRole()` unless it is a public route
- Always call `writeAudit()` for mutating operations
- Use `getTab()` for sheet access — never hardcode sheet references

---

## For More Details

See the full developer reference in [`.github/copilot-instructions.md`](/.github/copilot-instructions.md).