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

**You MUST complete ALL of these steps for ANY dashboard modification.** Do not skip any step.

### Before Making Changes
- [ ] **Download the current dashboard from Blockcell**: Use `download_site` with `site_name=trust-vendor-dashboard` and `directory_path=/tmp/trust-vendor-dashboard-deploy`. This creates `/tmp/trust-vendor-dashboard-deploy/index.html` — all edits go here.
- [ ] Read the downloaded HTML to understand existing structure
- [ ] Read the relevant Google Sheet tabs for context
- [ ] If adding a vendor, check Airtable Fin Ops for partner record and contract docs

### Dashboard HTML Changes
- [ ] Update data objects (JS variables) with correct values
- [ ] **Verify computed property ordering**: Any property that depends on another computed property MUST be defined AFTER its dependency. For example, `totalUsOnly` and `totalIntlOnly` must be computed before `utilPctUs`, `utilPctIntl`, or `utilPctTotal` that reference them. JavaScript does NOT hoist `var` assignments — using a property before its assignment line yields `undefined`/`NaN`.
- [ ] **Verify success rate precision**: The global `fmtPct()` function rounds to 1 decimal place. For vendors with very high success rates (>99.9%), this rounds to "100.0%" which is misleading. Use `.toFixed(2) + '%'` instead of `fmtPct()` for success rate metric cards to ensure rates like 99.98% display correctly rather than rounding to 100.0%.
- [ ] Verify projections and term math for every vendor touched:
  - `contractStart`/`contractEnd` = active term dates (NOT full multi-year contract)
  - `totalMonths` = length of active term in months (e.g., 12 for annual)
  - `elapsedMonths` = number of full months of data in active term (excl MTD)
  - `remainingMonths` = months left until active term end (incl current MTD month)
  - `committedAnnual` = commitment for the active term (e.g., 180K × 12 = 2,160,000)
  - `projectedEndOfTerm` = `totalCallsSum + avgMonthlyCalls * remainingMonths`
  - `projectedSpend` = `totalSpend + remainingMonths * (totalSpend / elapsedMonths)`
  - Utilization = `totalCallsSum / committedAnnual * 100` (active term only)
  - For multi-year contracts: use `activeTermStartIdx` to separate prior-term data; prior-term rows shown dimmed in tables but excluded from totals
  - All contract info cards use dynamic values (e.g., `remainingMonths + ' of ' + totalMonths`), NOT hardcoded strings
  - Contract Period text matches `contractStart` / `contractEnd` in the data object
- [ ] Update "Last refreshed" date on ALL pages (search for ALL instances of `last-refreshed`)
- [ ] Update vendor count on landing page if adding/removing vendors
- [ ] Update `grandTotalSpend` and `grandTotalCalls` if spend/volume data changed
- [ ] Update `queryMeta` object if refresh dates changed
- [ ] Update header menu (`#menu-dropdown`) — add/remove vendor links in the "Vendor Pages" section. Remove or update any `menu-item-sub` status labels (e.g., "Contract TBD", "Trial") once a real contract is in place — active vendors with contracts should have NO sub-label, matching the pattern of other active vendors. Preserve the "Documentation" section links (Google Sheet, Airtable, UI Spec, Goose Skill File → GitHub repo).
- [ ] Validate JS syntax: `node -c` on extracted script
- [ ] Deploy to Blockcell: upload `/tmp/trust-vendor-dashboard-deploy/` with `site_name=trust-vendor-dashboard`

### Google Sheet Updates (ALL tabs as applicable)
- [ ] **Vendors tab**: Add/update vendor row following the **Vendors Tab Column Schema** above. MUST read header + existing row first, build values by column letter A-T, verify after writing.
- [ ] **Pricing Tiers tab**: Add/update pricing rows with Airtable doc reference
- [ ] **Queries tab**: Add/update SQL templates (or note "N/A — invoice-based" for non-Snowflake vendors)
- [ ] **Monthly Data tab**: Add/update rows following the **Monthly Data Tab Column Schema** above (one row per vendor per month)
- [ ] **Refresh Log tab**: Append a new entry following the **Refresh Log Tab Column Schema** above

### Documentation Updates
- [ ] Update the UI/Layout Spec Google Doc if layout/structure changed

### Final Verification
- [ ] Dashboard loads correctly at the live URL
- [ ] All "Last refreshed" dates are consistent and correct
- [ ] Google Sheet data matches what's displayed in the dashboard
- [ ] Refresh Log has an entry for this change

## Checklist: Refreshing Vendor Data

### For Snowflake-sourced vendors (maxmind, idology, sentilink):
1. Read the SQL query from the **Queries** tab in the Google Sheet
2. Substitute `{start_date}` and `{end_date}` with appropriate dates
3. Execute in Snowflake
4. Update the **Monthly Data** tab with new results
5. Update the dashboard HTML data arrays
6. Update ALL "Last refreshed" dates in the HTML
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
8. Update ALL "Last refreshed" dates in the HTML
9. Update `queryMeta` object (lastRun, mtdDate, mtdDays, mtdTotalDays, mtdNote)
10. Validate JS, deploy to Blockcell
11. Append to **Refresh Log**

## Checklist: Adding a New Vendor

1. Check Airtable Fin Ops for partner record and **ALL linked documents**
   - **CRITICAL: A partner may have MULTIPLE agreements** (MSA, SOWs, amendments, order forms, etc.)
   - Always list ALL documents linked to the partner record in the Documents table (`tblWtAPWUsnZ7wleg`)
   - Read each document's AI-extracted fields: `Document Title (AI)`, `Document Summary (AI)`, `Key Contract Details (AI)`, `Effective Date (AI)`, `Agreement Expiration Date (AI)`, `Document Type (AI)`
   - Determine which agreement is the **currently active one** by examining effective dates, supersession clauses, and amendment chains
   - Common patterns: SOF #002 may replace SOF #001; amendments modify existing orders; newer MSAs may supersede older ones
   - **Never assume there is only one agreement** — always check the full document chain
   - Record the Airtable partner record URL and document record URLs for the Vendors tab
2. Search Snowflake (NATURALIZER, APP_CASH, APP_RISK, EVENTSTREAM2) for data sources
3. Determine billing methodology (usage-based vs flat fee vs prepaid)
4. Add to Google Sheet — **follow column schemas strictly**:
   - **Vendors tab**: Read header row + one existing row first. Build values A-T by column letter. Write. Read back and verify E/F/G/K/N.
   - **Pricing Tiers tab**: Add pricing rows
   - **Queries tab**: Add SQL template or "N/A — invoice-based"
5. Add to dashboard HTML:
   - Data object (after existing vendor data)
   - **Computed properties must be ordered by dependency** — define base sums (e.g., `totalUsOnly`, `totalIntlOnly`) BEFORE derived metrics (e.g., `utilPctUs`, `utilPctIntl`) that reference them
   - Include in `grandTotalSpend` / `grandTotalCalls`
   - HTML page structure (detail page div)
   - Routing in `handleRoute()` function
   - Landing page table row in `renderLanding()`
   - Detail render function (`renderXXXDetail()`)
   - Update vendor count on landing page
   - Update header menu (`#menu-dropdown`) — add vendor link in "Vendor Pages" section. Use a `menu-item-sub` label only for non-standard statuses (e.g., "Trial" for free trials, "Contract TBD" if contract is pending). Active vendors with confirmed contracts should have NO sub-label. Do NOT modify the "Documentation" section links (Google Sheet, Airtable, UI Spec, Goose Skill File → `https://github.com/alain-block/goose-recipes/blob/main/trust-vendor-dashboard/SKILL.md`).
   - Update ALL "Last refreshed" dates
6. Validate JS, deploy to Blockcell
7. Update Monthly Data tab (if usage data exists)
8. Append to Refresh Log
9. Update UI/Layout Spec Google Doc

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
- **Refresh buttons**: Snowflake-sourced vendors (MaxMind, IDOLOGY, SentiLink, Smarty, Socure) have a 🔄 Refresh button in their utilization cell on the landing page. Clicking it copies a `goose run --recipe ... --text "Refresh data for <vendor>"` command to the clipboard. The `refreshBtn()` helper function generates these buttons — it uses the `recipeUrl` variable which points to `https://github.com/alain-block/goose-recipes/tree/main/trust-vendor-dashboard`. When adding a new Snowflake-sourced vendor, include `refreshBtn('VendorName')` in the utilization cell. Invoice-based and CSV-sourced vendors do NOT get refresh buttons.
- MTD months: last month in each vendor's data array is typically MTD. Mark with `isMTD: true` and annotate in tables.
- Spend is NOT calculated for MTD months — tables show "—" and totals are labeled "Total (excl. MTD)".
- Flat-fee vendors (Forter, Fortra) have simpler detail pages — no charts, just metrics + contract info + spend table.
- The header menu (gear icon) contains links to the Google Sheet, Airtable, spec doc, and the Goose Skill File on GitHub (`https://github.com/alain-block/goose-recipes/blob/main/trust-vendor-dashboard/SKILL.md`). These links must survive routing changes (routing updates `#header-left`, not `#header-content`). If the GitHub repo URL ever changes, update the Goose Skill File link in the menu's "Documentation" section.
- **Always focus on the ACTIVE TERM** for utilization, spend, and projections. For multi-year contracts (e.g., NeuroID has two 12-month terms), `contractStart`/`contractEnd` and `totalMonths` refer to the **current active term**, not the full contract. Prior-term data may be shown in charts/tables for context but is excluded from totals. Use `activeTermStartIdx` to slice data arrays to the active term.
- **Projections are always "End of Term" (EOT)**: `projectedEndOfTerm = totalCallsSum + avgMonthlyCalls * remainingMonths`. Utilization % = `totalCallsSum / committedAnnual * 100` where `committedAnnual` = commitment for the active 12-month term.
- **NEVER hardcode** months remaining, contract periods, or "X of Y" strings in detail pages. Always use the data object properties (`remainingMonths`, `totalMonths`, `elapsedMonths`) so they stay correct when data is refreshed.
