---
name: trust-vendor-dashboard
description: Maintain and update the Trust Vendor Dashboard — a static web dashboard tracking risk & identity vendor spend, utilization, and BU allocation. Use this skill when adding vendors, refreshing data, or making any dashboard changes.
---

# Trust Vendor Dashboard — Maintenance Guide

## Key Artifacts

| Artifact | Location |
|---|---|
| **Live URL** | https://blockcell.sqprod.co/sites/trust-vendor-dashboard/ |
| **Blockcell site name** | `trust-vendor-dashboard` |
| **Dashboard HTML (working copy)** | `/tmp/trust-vendor-dashboard-deploy/index.html` |
| **Vendor Registry** | [Google Sheet](https://docs.google.com/spreadsheets/d/1TU-ZVPpTy08CBj1Ye5XyYDSAwfZuH2YFSRbAOFIQ3VQ/edit) |
| **UI/Layout Spec** | [Google Doc](https://docs.google.com/document/d/1OFbBOoDwwKk_6woiV5RytVT781rRVw1lBt4as3k3Ghg/edit) |
| **Airtable Fin Ops** | Base `appkZPvBBuQATjTSy` — Partners (`tblYah9316AMvESqf`), Documents (`tblWtAPWUsnZ7wleg`) |

## ⚠️ CRITICAL: Always Start by Downloading the Live Dashboard

The dashboard HTML is NOT stored in this repo or in Google Drive. The **live Blockcell site is the source of truth**.

Before making ANY changes, you MUST download the current dashboard:

```
1. Use Blockcell download_site action:
   - site_name: trust-vendor-dashboard
   - directory_path: /tmp/trust-vendor-dashboard-deploy
   - This downloads index.html to /tmp/trust-vendor-dashboard-deploy/index.html

2. All edits are made to: /tmp/trust-vendor-dashboard-deploy/index.html

3. After edits, deploy back to Blockcell:
   - site_name: trust-vendor-dashboard
   - directory_path: /tmp/trust-vendor-dashboard-deploy
```

This ensures any colleague can run the recipe from any machine — no local files required.

## Google Sheet Tabs

| Tab | Purpose |
|---|---|
| **Vendors** | Master registry — one row per vendor, Airtable links, contract refs, BU split, data source type |
| **Pricing Tiers** | All pricing tiers per vendor (from Airtable contract docs) |
| **Queries** | Full SQL templates with `{start_date}/{end_date}` placeholders, billing methodology |
| **Monthly Data** | Actual query results — one row per vendor per month, all metric columns |
| **Refresh Log** | Audit trail of every data refresh |

## ⚠️ CRITICAL: Vendors Tab Column Schema (MUST follow exactly)

**When writing ANY row to the Vendors tab, you MUST use this exact column-to-letter mapping.**
**NEVER write a row by assembling a flat list of values from memory. ALWAYS build the row by explicitly assigning each value to its column letter.**
**Before writing, READ the existing header row and at least one correct existing row to confirm the column layout has not changed.**

| Letter | Column Name | Example (NeuroID) |
|---|---|---|
| A | Vendor Name | Neuro-ID, Inc. |
| B | Slug | neuroid |
| C | Product | V4 Profile (Behavioral Analytics) |
| D | Status | Active |
| E | Owner | alain@block.xyz |
| F | Last Validated | 2026-03-16 |
| G | Data Source Type | snowflake |
| H | Dashboard Color | #6366f1 |
| I | Airtable Partner Record | https://airtable.com/appkZPvBBuQATjTSy/tblYah9316AMvESqf/recXXX |
| J | Airtable Document Record(s) | https://airtable.com/appkZPvBBuQATjTSy/pagWPjgxkluepfkbN/recXXX |
| K | Contract Start | 2024-06-01 |
| L | Contract End | 2026-05-31 |
| M | Renewal Terms | Auto-renewed once for 12 months... |
| N | Relationship DRI | alain@block.xyz |
| O | Business Entity | Square |
| P | Slack Channel | neuroid-square |
| Q | Partner Criticality | Tier 3 |
| R | Notes | Free-text notes about billing, methodology, etc. |
| S | Reference Links (label) | *(row 1 only — used for reference link labels)* |
| T | Reference Links (URL) | *(row 1 only — used for reference link URLs)* |

### Rules for writing Vendors tab rows:
1. **Always read the header row first** (`get_columns` or `get_values` on row 1) to confirm column positions.
2. **Always read at least one existing correct row** (e.g., row 2 for NeuroID) as a template.
3. **Build the values array by explicit column letter** — e.g., "A=SentiLink, B=sentilink, C=..., D=..., E=owner, F=date, ..."
4. **Count that you have exactly 20 values (A through T)** before writing. Use empty string `""` for any column that has no value.
5. **Never skip a column.** If a value is unknown, use `""` — do NOT omit it.
6. **After writing, immediately read back the row** and verify at least: E=Owner (email), F=Last Validated (date), G=Data Source Type, K=Contract Start, N=Relationship DRI.

### Monthly Data Tab Column Schema

| Letter | Column Name |
|---|---|
| A | Vendor Slug |
| B | Month |
| C | Is MTD |
| D | MTD Days |
| E | MTD Total Days |
| F | Billable Calls |
| G | Success |
| H | Failed |
| I | Verified |
| J | Failed Verify |
| K | Deceased |
| L | Unavailable |
| M | Secondary EIDV |
| N | Primary EIDV |
| O | Refresh Date |

### Queries Tab Column Schema

| Letter | Column Name |
|---|---|
| A | Vendor Slug |
| B | Query Name |
| C | Query Type |
| D | Source System |
| E | Database |
| F | Table |
| G | Timezone |
| H | Full SQL |
| I | Billable Unit Definition |
| J | Dedup Logic |
| K | Filters |
| L | Validation Status |
| M | Last Modified |
| N | Modified By |

### Refresh Log Tab Column Schema

| Letter | Column Name |
|---|---|
| A | Date |
| B | Vendor |
| C | Months Covered |
| D | Refreshed By |
| E | Notes |

## Checklist: EVERY Dashboard Change

### 1. Download
- [ ] Download from Blockcell: `download_site` → `site_name=trust-vendor-dashboard`, `directory_path=/tmp/trust-vendor-dashboard-deploy`

### 2. Edit (`/tmp/trust-vendor-dashboard-deploy/index.html`)
- [ ] Update data objects with correct values
- [ ] Update `lastRefreshed` in the vendor's data object
- [ ] Update `grandTotalSpend` / `grandTotalCalls` if spend/volume changed
- [ ] Update vendor count on landing page if adding/removing vendors
- [ ] Update menu, routing, summary table if adding/removing vendors (alphabetical order)
- [ ] Add vendor to `populateRefreshDates()` array if new

### 3. Validate & Deploy
- [ ] `node -c` on extracted script
- [ ] Deploy to Blockcell

### 4. Google Sheet (batch: read all headers first, then write all tabs, then verify)
- [ ] **Vendors tab** — build row by column letter A-T, verify E/F/G/K/N after write
- [ ] **Pricing Tiers** — add/update rows
- [ ] **Queries** — SQL template or `N/A — invoice-based` or `GDrive {type}`
- [ ] **Monthly Data** — one row per vendor per month
- [ ] **Refresh Log** — append entry

### 5. Verify
- [ ] Dashboard loads at live URL
- [ ] Data matches Google Sheet
- [ ] Refresh Log has entry

### ⚠️ Common Pitfalls (check if applicable)
- **Computed property ordering**: Dependencies must be defined BEFORE derived metrics. `totalUsOnly` before `utilPctUs`. JavaScript does NOT hoist `var` assignments.
- **Success rate precision**: Use `.toFixed(2) + '%'` instead of `fmtPct()` for rates >99.9% to avoid rounding to "100.0%".
- **Term math**: `contractStart`/`contractEnd` = active term only. `elapsedMonths` excludes MTD. `remainingMonths` includes current month. `committedAnnual` = active term commitment. Never hardcode "X of Y" strings — use data object properties.
- **Menu labels**: Active vendors with contracts have NO `menu-item-sub` label. Only use for "Trial" or "Contract TBD".

## Checklist: Refreshing Vendor Data

### For Snowflake-sourced vendors (maxmind, idology, sentilink):
1. Read the SQL query from the **Queries** tab in the Google Sheet
2. Substitute `{start_date}` and `{end_date}` with appropriate dates
3. Execute in Snowflake
4. Update the **Monthly Data** tab with new results
5. Update the dashboard HTML data arrays
6. Update `lastRefreshed` property in the vendor's data object (e.g., `lastRefreshed: 'March 30, 2026'`)
7. Update `queryMeta` object (lastRun, mtdDate, mtdDays, mtdTotalDays)
8. Validate JS, deploy to Blockcell
9. Append to **Refresh Log**

### For GDrive CSV-sourced vendors (neuroid):
1. Download the CSV file from Google Drive (see "Google Drive CSV Data Sources" section for file URL)
2. Parse CSV: filter to LIVE environment, include v4.1 + v6 API versions
3. Aggregate by month: sum HTTP 200 (→ Success) and HTTP 404 (→ Failed); Billable Calls = 200 + 404
4. Determine MTD: if the last date in the file is not the last day of its month, that month is MTD
5. Cover only months within the active contract term
6. Update the **Monthly Data** tab with new results
7. Update the dashboard HTML data arrays (months, totalCalls, completed, failed, isMTD, elapsedMonths, remainingMonths)
8. Update `lastRefreshed` property in the vendor's data object
9. Update `queryMeta` object (lastRun, mtdDate, mtdDays, mtdTotalDays, mtdNote)
10. Validate JS, deploy to Blockcell
11. Append to **Refresh Log**

## Checklist: Adding a New Vendor

### Step 1 — Upfront Intake (ASK FIRST, before doing anything)

Present this to the user in a single message and wait for answers before proceeding:

```
To add this vendor efficiently, please share what you know (skip anything you don't have):

1. **Data source**: Snowflake table/query, file (CSV/Excel/GSheet URL), or invoice-only?
2. **Contract info**: Start/end dates, commitment, pricing, renewal terms?
3. **Business unit split**: CashApp / Square / Afterpay percentages?
4. **Billing type**: Usage-based, flat fee, prepaid, or tiered?
5. **Multi-product?**: Does this vendor have multiple products we track separately?
6. **Anything else**: Slack channel, relationship DRI, notes?
```

**Why:** This eliminates 3-5 back-and-forth turns. The user often already knows the data source, billing type, and BU split — asking upfront avoids unnecessary Snowflake searches and Airtable deep-dives.

### Step 2 — Parallel Research (run simultaneously)

Run these lookups **in parallel** — they are independent read-only operations:

| Track A: Airtable | Track B: Data Source |
|---|---|
| Search partner in `tblYah9316AMvESqf` | Based on user's answer from Step 1: |
| List linked docs from `tblWtAPWUsnZ7wleg` | **(a) Snowflake** → run the query |
| Triage docs (see below) | **(b) File** → download and parse |
| | **(c) Invoice** → skip (no data to fetch) |
| | **(d) Not sure** → search Snowflake tables |

**Smart Airtable document triage** (avoid reading every field of every doc):
1. List ALL linked documents but fetch only: `Document Title (AI)`, `Document Type (AI)`, `Effective Date (AI)`, `Agreement Expiration Date (AI)`
2. Sort by effective date descending
3. Deep-read ONLY the 1-2 most recent/relevant docs (active SOF, current MSA, latest amendment)
4. Skip older superseded agreements unless the chain is ambiguous
5. Record partner URL and active document URL(s) for the Vendors tab

### Step 3 — Determine Vendor Type

Based on intake answers + research, classify the vendor:

| Type | Characteristics | Dashboard Pattern |
|---|---|---|
| **usage-snowflake** | Query counts from Snowflake | Volume chart + pace gauge + detail table |
| **usage-gdrive** | Query counts from CSV/Excel/GSheet | Volume chart + pace gauge + detail table |
| **spend-only** | Dollar amounts only (no txn counts) | Spend chart + detail table (no pace gauge) |
| **invoice-flat** | Fixed fee, no usage data | Metrics + contract info only (no charts) |

### Step 4 — Dashboard HTML

1. Download from Blockcell → `/tmp/trust-vendor-dashboard-deploy/index.html`
2. Add data object in **alphabetical position** among existing vendor vars (include `lastRefreshed`, `buSplit`, `slug`)
3. Add to `grandTotalSpend` / `grandTotalCalls` (if applicable)
4. Add detail page div: `<div class="page" id="page-detail-{slug}">` with standard containers (follow Standard Detail Page Layout below)
5. Add `renderXXXDetail()` function — use `renderBU(containerId, v.buSplit, v.totalSpend, note)` for BU attribution (shared helper, already defined)
6. Add routing in `handleRoute()` — call `renderXXXDetail()`
7. Add menu entry in `#menu-dropdown` (alphabetical position)
8. Add summary table row in `renderLanding()` (alphabetical position)
9. Add to `populateRefreshDates()` vendors array
10. Add to resize handler `setTimeout` block
11. Update vendor count on landing page
12. Validate JS: `node -c`
13. Deploy to Blockcell

### Step 5 — Google Sheet (batch writes)

**Read phase** (parallel): Read header row + one template row from ALL tabs in one pass.
**Write phase** (parallel where possible): Write all tabs, then verify.

| Tab | Action |
|---|---|
| **Vendors** | Add row (columns A-T, build by letter, verify E/F/G/K/N after write) |
| **Pricing Tiers** | Add pricing rows with Airtable doc reference |
| **Queries** | Add SQL template, or `N/A — invoice-based`, or `GDrive {type}` |
| **Monthly Data** | Add rows (one per month) if usage data exists |
| **Refresh Log** | Append entry |

### Step 6 — Finalize

- [ ] Dashboard loads correctly at live URL
- [ ] New vendor page follows Standard Detail Page Layout
- [ ] `lastRefreshed` property set in data object
- [ ] Google Sheet data matches dashboard
- [ ] Push updated SKILL.md to GitHub if vendor table changed

### Type-Specific Quick Reference

**Usage-based (Snowflake or GDrive):**
```
Data object: months, totalCalls, completed, failed, isMTD, buSplit, commitment, pricing, lastRefreshed, slug
Render function: renderXXXDetail() — metrics grid, renderBU(), renderPaceGauge(), bar chart, detail table
Sheet: All 5 tabs
```

**Spend-only (dollar amounts, no txn counts):**
```
Data object: months, account arrays (spend per account), isMTD, buSplit, pricing, lastRefreshed, slug
Render function: renderXXXDetail() — metrics grid, renderBU(), spend chart (no pace gauge), detail table
Sheet: Vendors + Pricing + Refresh Log (no Queries, Monthly Data uses spend columns)
```

**Invoice/flat-fee (no usage data at all):**
```
Data object: annualFee, monthlyFee, contractStart/End, buSplit, lastRefreshed, slug
Render function: renderXXXDetail() — metrics grid, renderBU(), contract info, spend table (no charts)
Sheet: Vendors + Pricing + Refresh Log only
```

### Shared Helper Functions

| Function | Purpose | Usage |
|---|---|---|
| `renderBU(containerId, buSplit, totalSpend, note)` | Renders BU attribution bar + legend | All 10 vendors with BU containers |
| `renderPaceGauge(containerId, label, used, committed, elapsed, total, paceColor, accentColor)` | Renders utilization gauge with pace marker | Usage-based vendors with commitments |
| `renderBarChart(canvasId, labels, data, opts)` | Renders bar chart on canvas | Volume/spend charts |
| `renderStackedBarChart(canvasId, labels, datasets, opts)` | Renders stacked bar chart | Multi-account spend charts (e.g., TLOxp) |
| `populateRefreshDates()` | Fills all "Last refreshed" spans from data objects | Called once at init |

## Current Vendors (as of March 2026)

| # | Vendor | Slug | Type | BU | Data Source |
|---|---|---|---|---|---|
| 1 | NeuroID | neuroid | Usage-based ($0.11/call) | 100% Square | Google Drive CSV (vendor portal export): https://drive.google.com/file/d/1lxQN1a5Ygw9SnEBbfaoXrFIPhQmnnHAQ/view |
| 2 | MaxMind | maxmind | Tiered + min fee | TBD | Snowflake: NATURALIZER.public.linkages |
| 3 | IDOLOGY (GBG) | idology | All-or-nothing tiered | 100% CashApp | Snowflake: APP_CASH.HEALTH.IDENTITY_IDV_VERIFICATIONS |
| 4 | Forter | forter | Flat $10K/mo | 100% Square | Invoice (not in Snowflake) |
| 5 | Fortra | fortra | Flat $73K/yr | Equal 33.3% split | Invoice (not in Snowflake) |
| 6 | SentiLink | sentilink | Free trial ($0) | 100% CashApp | Snowflake: NATURALIZER.public.linkages |
| 7 | Smarty | smarty | Prepaid $301K/yr (1B US + 500M Intl lookups) | 60% CashApp, 40% Square | Snowflake: EVENTSTREAM2.CATALOGS.ADDRESS_VENDOR_REQUEST |
| 8 | Socure | socure | Usage-based $0.10/call, $3.8M annual min | 100% CashApp | Snowflake: APP_CASH.HEALTH.IDV_VENDOR_EVALUATION_VERIFICATIONS |
| 9 | NetCraft | netcraft | Flat $375,227/yr | Equal 33.3% split | Invoice (not in Snowflake) |
| 10 | TransUnion — Risk & Fraud | tu-risk | 720K/yr @ $0.135, min $97.2K/yr | 100% Square | GDrive Excel (vendor-provided) |
| 11 | TransUnion — MPIC | tu-mpic | 2.5M/yr @ $0.104, min $260K/yr | 100% Square | GDrive Excel (vendor-provided) |
| 12 | TransUnion — TLOxp | tu-tloxp | TLOxp Online tiered $1.25→$0.88/txn, $12.5K/mo min | 80% CashApp, 20% Square | GDrive billing statement (vendor-provided) |

## Google Drive CSV Data Sources

### NeuroID
- **File**: https://drive.google.com/file/d/1lxQN1a5Ygw9SnEBbfaoXrFIPhQmnnHAQ/view
- **Format**: CSV export from NeuroID vendor portal (daily rows)
- **Columns**: Row#, Date, Environment, Organization, Site, Form ID, API Version, HTTP 200 Count, HTTP 401 Count, HTTP 404 Count
- **Filters**: Environment = LIVE only; API Version = v4.1 and v6 (include both)
- **Billable calls**: HTTP 200 count + HTTP 404 count (sum per month)
- **Success column in Monthly Data**: HTTP 200 count
- **Failed column in Monthly Data**: HTTP 404 count
- **Note**: Numbers in CSV use comma formatting (e.g., "3,019") — must parse as integers
- **Contract**: Jun 1, 2024 – May 31, 2026 (two 12-month terms). Active term: Jun 1, 2025 – May 31, 2026.
- **Active term rule**: `contractStart`/`contractEnd`/`totalMonths` always refer to the active 12-month term. Prior-term data (e.g., Mar–May 2025) is shown in charts for context but excluded from utilization/spend totals. Use `activeTermStartIdx` to mark where the active term begins in the data arrays.
- **Refresh process**: User updates the GDrive file periodically. To refresh, download the file, aggregate LIVE v4.1+v6 rows by month, update Monthly Data tab and dashboard HTML. Always verify `activeTermStartIdx` is correct for the current active term.

## Snowflake Config
- Account: SQUARE
- Warehouse: ADHOC__SMALL (use ADHOC__LARGE for big queries)
- Timezone: America/New_York for NATURALIZER queries
- Always use fully qualified table names

## Sharing & Collaboration

The recipe and SKILL.md live in a public GitHub repo. The recipe reads `SKILL.md` from the same directory using `{{ recipe_dir }}/SKILL.md`.

| Artifact | Location |
|---|---|
| **GitHub Repo** | [alain-block/goose-recipes](https://github.com/alain-block/goose-recipes) |
| **Recipe** | `trust-vendor-dashboard/recipe.yaml` |
| **Skill** | `trust-vendor-dashboard/SKILL.md` (this file) |
| **Local copy** | `~/.config/goose/skills/trust-vendor-dashboard/SKILL.md` |

### How colleagues run this recipe
```bash
goose run --recipe https://github.com/alain-block/goose-recipes/tree/main/trust-vendor-dashboard
```

### Updating the skill
1. Edit `SKILL.md` in the repo (clone → edit → push, or edit on GitHub directly)
2. Also update the local copy: `~/.config/goose/skills/trust-vendor-dashboard/SKILL.md`
3. Colleagues get the latest version automatically next time they run the recipe

## Important Notes
- MTD months: last month in each vendor's data array is typically MTD. Mark with `isMTD: true` and annotate in tables.
- Spend is NOT calculated for MTD months — tables show "—" and totals are labeled "Total (excl. MTD)".
- Flat-fee vendors (Forter, Fortra) have simpler detail pages — no charts, just metrics + contract info + spend table.
- The header menu (gear icon) contains links to the Google Sheet, Airtable, spec doc, and the Goose Skill File on GitHub (`https://github.com/alain-block/goose-recipes/blob/main/trust-vendor-dashboard/SKILL.md`). These links must survive routing changes (routing updates `#header-left`, not `#header-content`). If the GitHub repo URL ever changes, update the Goose Skill File link in the menu's "Documentation" section.
- **Always focus on the ACTIVE TERM** for utilization, spend, and projections. For multi-year contracts (e.g., NeuroID has two 12-month terms), `contractStart`/`contractEnd` and `totalMonths` refer to the **current active term**, not the full contract. Prior-term data may be shown in charts/tables for context but is excluded from totals. Use `activeTermStartIdx` to slice data arrays to the active term.
- **Projections are always "End of Term" (EOT)**: `projectedEndOfTerm = totalCallsSum + avgMonthlyCalls * remainingMonths`. Utilization % = `totalCallsSum / committedAnnual * 100` where `committedAnnual` = commitment for the active 12-month term.
- **NEVER hardcode** months remaining, contract periods, or "X of Y" strings in detail pages. Always use the data object properties (`remainingMonths`, `totalMonths`, `elapsedMonths`) so they stay correct when data is refreshed.
- **Projected End-of-Term (EOT) Spend is a REQUIRED metric** for every vendor detail page. Always show what the vendor will cost by contract end at the current pace. For usage-based vendors paying minimums, this is the annual minimum. For usage-based vendors above minimums, extrapolate from the monthly average. For flat-fee vendors, this is the annual fee. Include this in the metrics grid for all vendors.
- **Vendors with multiple products/service lines** (e.g., TransUnion has Risk & Fraud, MPIC, and TLOxp) should be **split into separate dashboard entries** — one per product. Each product has its own contract term, commitment, pricing, and utilization story, so treating them as independent vendors keeps the code simple and consistent (no special-case multi-service logic). To maintain the vendor relationship:
  - Name them with a shared prefix: "TransUnion — Risk & Fraud", "TransUnion — MPIC", "TransUnion — TLOxp"
  - Place them adjacent in the menu and summary table
  - Add "See Also" links on each detail page pointing to sibling entries
  - Use the same Slack channel and MSA date on all
  - Each entry follows the exact same standard vendor pattern as every other vendor
- **Spend-only vendors** (no query counts available): Some vendors only provide billing statements with dollar amounts, not transaction counts. For these, track monthly spend per account, show a spend bar chart instead of a volume chart, and skip utilization/pace gauges. The detail table shows account-level breakdown and BU attribution. Example: TransUnion — TLOxp.
- **Bar charts MUST show only active-term months.** Each vendor's chart shows only the months within its active contract term. The data object should contain only active-term data (not the full historical window). Elapsed months, remaining months, and utilization % must all be computed from the active term, not the full data window. This prevents misleading utilization percentages.
- **Pace gauge** (`renderPaceGauge` helper): Shows a progress bar of queries used vs committed, with a vertical marker for expected pace based on time elapsed. Color-coded status: 🟢 ≥80% of expected pace, 🟡 50-80%, 🔴 <50%. Use for any vendor with a volume commitment and minimum fee.

### Standard Detail Page Layout (REQUIRED)

Every vendor detail page MUST follow this exact HTML structure and element ordering. Do NOT deviate from this layout — it ensures visual consistency across all vendor pages.

```
1. Contract banner      — colored card with border-left accent, key terms at a glance
2. Metrics grid         — 7 metric cards (use class "metrics-grid")
3. Pace gauge           — full-width commitment utilization bar (if vendor has volume commitment)
4. two-col row          — Contract Information (left) | Business Unit Attribution (right)
5. Volume/Spend chart   — FULL-WIDTH card with chart legend + canvas (height="260")
6. Detail table         — full-width card with scrollable table
7. Last refreshed       — timestamp
```

**HTML template:**
```html
<div class="page" id="page-detail-{slug}">
  <!-- 1. Contract banner -->
  <div class="card" style="background:{bgColor};border-left:4px solid {accentColor};margin-bottom:16px">
    <div class="card-title" style="color:{titleColor}">{icon} Active Contract — {renewal type}</div>
    <div style="font-size:13px;color:{textColor};line-height:1.6">
      <strong>MSA:</strong> ... | <strong>Term:</strong> ... | <strong>Bill To:</strong> ...<br>
      <strong>Commitment:</strong> ... | <strong>Annual Min:</strong> ... | <strong>Pricing:</strong> ...<br>
      <em>Renewal terms. See also links if multi-product vendor.</em>
    </div>
  </div>
  <!-- 2. Metrics grid -->
  <div class="metrics-grid" id="{prefix}-metrics-grid"></div>
  <!-- 3. Pace gauge (if applicable) -->
  <div id="{prefix}-gauge" style="padding:0 24px 16px"></div>
  <!-- 4. Two-col: Contract Info + BU Attribution -->
  <div class="two-col">
    <div class="card">
      <div class="card-title">Contract Information</div>
      <div class="contract-grid" id="{prefix}-contract-info"></div>
    </div>
    <div class="card">
      <div class="card-title">Business Unit Attribution</div>
      <div class="gauge-container" id="{prefix}-bu-container"></div>
    </div>
  </div>
  <!-- 5. Volume/Spend chart — ALWAYS full-width, NEVER inside two-col -->
  <div class="card">
    <div class="card-title">Monthly Volume/Spend</div>
    <div class="chart-legend" id="{prefix}-volume-legend"></div>
    <div class="chart-container"><canvas id="{prefix}-chart-volume" height="260"></canvas></div>
  </div>
  <!-- 6. Detail table -->
  <div class="card">
    <div class="card-title">Monthly Detail</div>
    <div class="scroll-table"><table class="detail-table" id="{prefix}-detail-table"></table></div>
  </div>
  <!-- 7. Last refreshed (populated dynamically from data object) -->
  <div class="last-refreshed">Last refreshed: <span id="refresh-{slug}"></span></div>
</div>
```

**Last-refreshed dates are DYNAMIC.** Each vendor data object has a `lastRefreshed` property (e.g., `lastRefreshed: 'March 30, 2026'`). The `populateRefreshDates()` function runs on page load and fills every `<span id="refresh-{slug}">` from the corresponding data object. The summary page shows the most recent date across all vendors. When refreshing a vendor's data, update ONLY the `lastRefreshed` property in that vendor's data object — do NOT edit the HTML spans directly.

**Ordering rule:** Vendors are listed **alphabetically** in the menu, summary table, and detail page HTML divs. When adding a new vendor, insert it in the correct alphabetical position in all three places. Multi-product vendors (e.g., TransUnion — MPIC, TransUnion — Risk, TransUnion — TLOxp) sort by their full name including the product suffix.

**Common mistakes to avoid:**
- ❌ Putting the volume chart inside a `two-col` — it gets cramped and unreadable
- ❌ Omitting the contract banner — the page looks incomplete without the colored summary card
- ❌ Putting BU Attribution or Pace Gauge in a second `two-col` row below the chart — they belong above it
- ❌ Missing chart legend — always include a legend div above the canvas
- ❌ Using `height="240"` on canvas — standard is `height="260"`
- ❌ Forgetting to add the vendor to the resize handler in the `setTimeout` block at the end of the file
- ❌ Adding a new vendor at the end instead of in alphabetical order
