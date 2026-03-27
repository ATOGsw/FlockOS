# Single-Sheet Architecture — Feasibility Analysis

> **Status:** Planning / Discussion  
> **Date:** March 27, 2026  
> **Scope:** Consolidating all 4 Google Sheets into 1, with an expansion model for growth

---

## Current Architecture (4 Sheets, 4 GAS Projects)

| Gospel | Role | Tabs | Avg Cols | GAS File Lines |
|--------|------|------|----------|----------------|
| **John** (Flock) | CRM — Members, Care, Groups, etc. | 82 | ~14 | ~10,000 |
| **Mark** (Missions) | Missions & Persecution tracking | 8 | ~22 | ~6,500 |
| **Luke** (Extra) | Statistics & Analytics | 3 | ~30 | ~2,500 |
| **Matthew** (App) | Public Content (Devotionals, etc.) | 12 | ~10 | ~1,137 |
| **Total** | | **105** | ~15.5 | **~20,137** |

Each sheet has its own GAS project with a separate `doGet()` entry point and deployed Web App URL.  
The frontend (`TheVine`) routes to 4 separate endpoint branches.

---

## What "Single Sheet" Means

**One Google Spreadsheet** containing all 105 tabs.  
**One GAS project** with one `doGet()` entry point.  
**One deployed Web App URL** that handles every API action.

---

## Google Sheets Limits That Matter

| Limit | Value | Our Usage (Estimated) |
|-------|-------|-----------------------|
| Cells per spreadsheet | **10,000,000** | ~1,627 cols × rows |
| Tabs per spreadsheet | **200** (soft) | **105** tabs (52% used) |
| Rows per tab (practical) | ~50,000–100,000 | Varies by church size |
| GAS execution time | **6 minutes** per call | Single reads: < 5 sec |
| GAS daily triggers | 90 min/day | N/A (Web App, no triggers) |
| Code.gs file size | No hard limit | ~20K lines combined |
| Deployed Web App URLs | 1 per project | **1** (down from 4) |

### Cell Budget Math

At **1,627 total columns** across 105 tabs:

| Rows per tab | Total Cells | % of 10M Limit |
|--------------|-------------|-----------------|
| 100 | 162,700 | 1.6% |
| 500 | 813,500 | 8.1% |
| 1,000 | 1,627,000 | 16.3% |
| 2,500 | 4,067,500 | 40.7% |
| 5,000 | 8,135,000 | **81.4%** |

**Bottom line:** A small-to-medium church (< 2,500 members) has plenty of room. A large church pushing 5,000+ rows per tab would hit the ceiling.

---

## What We Already Have That Makes This Easier

### 1. TheVine Action Routing (the_true_vine.js)

TheVine already maps every API call to an action string like `members.list`, `sermons.create`, `missions.trips.list`. Today it picks which *endpoint* (Matthew/Mark/Luke/John URL) to send it to. In a single-sheet world, all actions go to the same URL — **the routing simplifies**.

```
BEFORE:  TheVine → pick endpoint branch → send to 1 of 4 URLs
AFTER:   TheVine → send everything to 1 URL
```

### 2. TheWellspring Local Resolver (the_wellspring.js)

TheWellspring already resolves *all* actions locally against IndexedDB. It doesn't care how many sheets or springs exist — it just maps `action → tab`. **No change needed for local/offline mode.**

### 3. TheWell Drive Sync (the_well.js)

Currently syncs 4 `.xlsx` files (one per spring). In single-sheet mode, it would sync **1 `.xlsx` file** — simpler, fewer network calls, one OAuth scope.

### 4. Setup.gs Already Creates All Tabs Programmatically

`setupExpansion()` in John's Setup.gs already creates 82 tabs. The other 3 sheets have their own setup functions. Merging them into one `setupAll()` that creates all 105 tabs in one spreadsheet is straightforward.

---

## The Unified doGet() — How It Would Work

One combined Code.gs (~20K lines) with a single router:

```javascript
function doGet(e) {
  var params = (e && e.parameter) ? e.parameter : {};
  var action = String(params.action || '').trim();

  // Health check (no auth)
  if (action === 'health') return asJson({ status: 'ok', tabs: 105 });

  // Public actions (no auth required)
  if (action === 'app.tab')        return asJson(handleAppTab_(params));
  if (action === 'prayer.submit')  return asJson(handlePrayerSubmit_(params));
  if (action === 'prayer.listPublic') return asJson(handlePrayerListPublic_(params));

  // Everything else requires auth
  var auth = requireAuth_(params);

  // Route by prefix
  if (action.startsWith('missions.'))  return asJson(routeMissions_(action, params, auth));
  if (action.startsWith('stats.'))     return asJson(routeStatistics_(action, params, auth));

  // Default: FLOCK CRM actions (80% of traffic)
  return asJson(routeFlock_(action, params, auth));
}
```

**Key change:** Auth validation (`requireAuth_`) no longer cross-calls John's URL — it reads directly from the same spreadsheet. This **eliminates the network hop** that Mark and Luke currently make to validate tokens.

---

## The Expansion Model — "Loading" Additional Sheets

This is the most interesting part. When one sheet starts filling up, you don't migrate — you **add a shard**.

### How It Works

```
┌─────────────────────────────────────┐
│  PRIMARY SHEET (Sheet A)            │
│  • All 105 tabs                     │
│  • Active data (current year)       │
│  • All writes go here               │
│  • GAS Code.gs lives here           │
└──────────────┬──────────────────────┘
               │  When approaching limits...
               ▼
┌─────────────────────────────────────┐
│  ARCHIVE SHEET (Sheet B)            │
│  • Same 105-tab structure           │
│  • Older/archived data              │
│  • Read-only from GAS perspective   │
│  • Linked via ARCHIVE_SHEET_ID      │
└─────────────────────────────────────┘
               │  If Sheet B fills...
               ▼
┌─────────────────────────────────────┐
│  ARCHIVE SHEET (Sheet C)            │
│  • Same pattern continues           │
└─────────────────────────────────────┘
```

### Archive Configuration (Script Properties)

```
SHEET_ID          = "primary_sheet_id"         ← All writes
ARCHIVE_SHEET_IDS = "sheet_b_id,sheet_c_id"    ← Read-only shards
```

### Archive-Aware Reads

```javascript
function listMembers_(params, auth) {
  // 1. Read from primary sheet (current/active data)
  var primary = readTab_('Members', db());

  // 2. If archive sheets exist and params.includeArchive !== false
  var archives = getArchiveSheetIds_();
  if (archives.length && params.includeArchive !== false) {
    archives.forEach(function(sheetId) {
      var archiveDb = SpreadsheetApp.openById(sheetId);
      var older = readTab_('Members', archiveDb);
      primary = primary.concat(older);
    });
  }

  return { data: primary };
}
```

### Archive Lifecycle

| Phase | Trigger | Action |
|-------|---------|--------|
| **Green** | < 50% cell budget | Normal operation |
| **Yellow** | 50–75% cell budget | Dashboard warning, suggest archive |
| **Orange** | 75–90% cell budget | Auto-archive prompt in Control Panel |
| **Red** | > 90% cell budget | Block new row creation, force archive |

### What Gets Archived

Not all tabs grow equally. The heavy growers are:

| Tab | Why It Grows | Archive Strategy |
|-----|-------------|-----------------|
| AuditLog | Every action logged | Archive entries > 6 months |
| AuthAudit | Every login/auth event | Archive entries > 6 months |
| CommsMessages | Every message sent | Archive read/old threads > 1 year |
| InteractionLedger | Every pastoral touch | Archive entries > 1 year |
| Giving | Every donation | Archive by fiscal year |
| Attendance | Every check-in | Archive by calendar year |
| PrayerRequests | Every submission | Archive resolved > 6 months |
| ToDo | Every task | Archive completed > 3 months |

### The Archive Action

One button in Control Panel:

```
[Archive Data Older Than: [6 months ▾]]  [Preview]  [Archive Now]
```

1. **Preview** — Shows row counts that would move per tab
2. **Archive Now** — For each eligible tab:
   - Copy old rows to Archive Sheet (same tab name)
   - Delete old rows from Primary Sheet
   - Log the operation to AuditLog
3. Primary sheet cell count drops immediately

---

## Frontend Changes Required

### TheVine (the_true_vine.js)

```javascript
// BEFORE: 4 endpoint branches
const APP_ENDPOINTS = [{ PRIMARY: '...', SECONDARY: '...', TERTIARY: '...' }];
const FLOCK_ENDPOINTS = [{ PRIMARY: '...', SECONDARY: '...', TERTIARY: '...' }];
const MISSIONS_ENDPOINTS = [{ PRIMARY: '...', SECONDARY: '...', TERTIARY: '...' }];
const EXTRA_ENDPOINTS = [{ PRIMARY: '...', SECONDARY: '...', TERTIARY: '...' }];

// AFTER: 1 endpoint
const ENDPOINTS = [{ PRIMARY: '...', SECONDARY: '...', TERTIARY: '...' }];
```

Every `TheVine.flock()`, `TheVine.app()`, `TheVine.missions()`, `TheVine.extra()` call would go to the same URL. The action string already differentiates everything.

**Alternative:** Keep the 4 branch methods for code clarity but point them all at the same endpoint. Zero frontend refactoring needed beyond changing the URL constants.

### TheWellspring (the_wellspring.js)

**Minimal change.** The SPRINGS concept could collapse to one spring, or stay as 4 logical springs pointing at different tab groups within the same IndexedDB store. The resolver doesn't care — it maps actions to tab names regardless.

### TheWell (the_well.js)

**Simplifies.** Sync 1 `.xlsx` file instead of 4. File matching by name pattern becomes unnecessary.

---

## What You Gain

| Benefit | Impact |
|---------|--------|
| **1 deployment** instead of 4 | One URL to manage, one GAS project to update |
| **No cross-project auth calls** | Mark/Luke currently call John to validate tokens — eliminated |
| **Simpler Wellspring sync** | 1 file instead of 4 |
| **Faster auth** | Token validation reads from same spreadsheet — no network hop |
| **Easier backup** | Copy 1 sheet instead of 4 |
| **Unified setup** | 1 `setupAll()` creates everything |
| **Single audit trail** | All AuditLog entries in one place |

## What You Lose

| Trade-off | Impact |
|-----------|--------|
| **Cell budget shared** | 10M cells across all 105 tabs instead of 10M × 4 |
| **Higher archive frequency** | Likely need to archive every 6–12 months for active churches |
| **Single point of failure** | If the sheet corrupts, everything is affected (mitigated by Drive versioning + Wellspring local backup) |
| **Larger Code.gs** | ~20K lines in one file — manageable but dense |
| **GAS quota concentration** | All API calls hit one project's quotas instead of spreading across 4 |
| **Concurrent edit risk** | GAS has a lock contention issue with many simultaneous writers to one sheet |

---

## Risk Assessment

### Low Risk
- Tab count (105 of 200 limit) — plenty of headroom
- Code size (~20K lines) — GAS handles this fine
- Setup complexity — already solved patterns

### Medium Risk
- Cell budget for large churches (> 2,000 members with years of history)
- Mitigated by the archive expansion model

### Higher Risk
- **Lock contention**: Google Sheets has row-level locking issues when multiple GAS executions write simultaneously. With 4 sheets, writes to Members don't block writes to Missions. With 1 sheet, they share a lock.
- **Mitigation**: Use `LockService.getScriptLock()` with short hold times, batch writes (already implemented in Discipleship.gs pattern), and the archive model to keep the primary sheet lean.

---

## Migration Path (If We Proceed)

### Phase 1: Merge the Code
1. Combine all 4 `.gs` files into one `FlockOS_Combined.gs`
2. Unify `doGet()` with prefix-based routing
3. Inline the auth validation (remove cross-project calls)
4. Merge all 4 `setup*()` functions into `setupAll()`

### Phase 2: Merge the Data  
1. Create new single spreadsheet
2. Run `setupAll()` to create all 105 tabs with headers
3. Copy data from existing 4 sheets into matching tabs
4. Deploy new Web App URL

### Phase 3: Update Frontend
1. Point all 4 endpoint branches at the new single URL
2. Update TheWell to sync 1 file
3. Update service worker cache version
4. Test every module end-to-end

### Phase 4: Add Archive System
1. Build `archiveData_()` function in Code.gs
2. Build `readWithArchive_()` wrapper for list handlers
3. Add Control Panel UI for archive management
4. Add cell budget health indicator to dashboard

---

## The "Load a Sheet" UX Concept

From the user's perspective in the Control Panel:

```
┌─────────────────────────────────────────────────────┐
│  DATABASE HEALTH                                    │
│                                                     │
│  Primary Sheet: FlockOS_Main                        │
│  Tabs: 105 / 200                                    │
│  Cell Usage: ████████░░ 3.2M / 10M (32%)           │
│  Status: ● HEALTHY                                  │
│                                                     │
│  ─────────────────────────────────────────────────  │
│                                                     │
│  Archive Sheets:                                    │
│  ┌─────────────────────────────────────────────┐    │
│  │ 📦 FlockOS_Archive_2025                     │    │
│  │    Loaded: Jan 2026 | Rows: 14,200          │    │
│  │    Coverage: Jan 2024 – Dec 2025            │    │
│  │    Status: ● Read-Only                      │    │
│  └─────────────────────────────────────────────┘    │
│                                                     │
│  [+ Load Archive Sheet]  [Archive Old Data]         │
│                                                     │
│  ─────────────────────────────────────────────────  │
│                                                     │
│  Archive Settings:                                  │
│  Auto-archive threshold: [75% ▾]                    │
│  Archive data older than: [6 months ▾]              │
│  Include in searches:     [✓ Yes]                   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**"+ Load Archive Sheet"** opens a dialog:
1. Paste the Google Sheet ID of the archive
2. App validates it has the expected tab structure
3. Adds it to `ARCHIVE_SHEET_IDS` in Script Properties
4. Archive data is now searchable from the app

---

## Verdict

**It's very feasible.** The hardest work — unified action routing, local data resolution, setup automation — is already done. The main risk is lock contention for high-traffic churches, and the cell budget requires an archive discipline that doesn't exist in the current 4-sheet model (where you'd likely never hit 10M cells per sheet).

The archive expansion model ("load a sheet") is the right answer to the capacity constraint. It turns a limitation into a feature: churches *should* be archiving old data periodically, and this architecture makes that a first-class operation instead of an afterthought.

**Recommendation:** Worth pursuing. Start with Phase 1 (merge the code) as a proof of concept — it's reversible and reveals any hidden coupling between the 4 projects.
