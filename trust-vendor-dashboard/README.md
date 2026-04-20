# Trust Vendor Dashboard

Maintain and update the [Trust Vendor Dashboard](https://blockcell.sqprod.co/sites/trust-vendor-dashboard/) — a static web dashboard tracking risk & identity vendor spend, utilization, and BU allocation.

## Quick Start

```bash
goose run --recipe https://github.com/alain-block/goose-recipes/tree/main/trust-vendor-dashboard
```

Or clone and run locally:

```bash
git clone https://github.com/alain-block/goose-recipes.git
goose run --recipe goose-recipes/trust-vendor-dashboard
```

## What it does

- Add new vendors to the dashboard
- Refresh monthly data from Snowflake
- Update contract details and pricing
- Deploy changes to Blockcell

## Required Extensions

The recipe will configure these automatically:
- **Google Drive** — read/write vendor registry, skill doc, contracts
- **Snowflake** — query vendor usage data
- **Blockcell** — deploy the dashboard
- **Airtable** — read partner records and contract documents

## Skill Document

The full maintenance guide (procedures, checklists, column schemas) lives in a shared Google Doc:
[Trust Vendor Dashboard — SKILL.md](https://docs.google.com/document/d/1vr0_K2gE0KtH-AFhv1eHU2xhlmJo1NlKzm0F4-kYGRs/edit)

Edit the Google Doc directly to update procedures — all collaborators get changes immediately.
