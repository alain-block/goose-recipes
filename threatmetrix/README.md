# LexisNexis ThreatMetrix — Goose Skill & Recipe

A Goose skill and recipe for managing the LexisNexis ThreatMetrix vendor relationship at Block. Provides complete contract knowledge, invoice tracking, usage analysis, and overage forecasting across all three Block business units (Square, Cash App, and Afterpay).

## What's Included

| File | Purpose |
|---|---|
| `SKILL.md` | Complete vendor knowledge base — contracts, pricing, invoices, usage data, forecasts, contacts |
| `recipe.yaml` | Goose recipe configuration for interactive TMX vendor management sessions |

## What the Skill Covers

### Contract Knowledge
- **Full contract chain** from the 2019 Master Terms through the current active agreements
- **Base Schedule A** (546427.5v8) — 162M transactions @ $0.00887, effective Apr 1, 2025
- **Schedule A Amendment** (571588.2v4) — 377M additional transactions @ $0.00747, signed Dec 2025
- **Combined commitment:** 539M transactions, $4,579,275.30 total value
- **Term:** April 1, 2025 – September 30, 2026 (18 months)
- Overage and term extension pricing

### Entity & Billgroup Mapping
| Billgroup | Block Entity | Allocation |
|---|---|---|
| 2038139 | Square | 360M transactions |
| 21526289 | Cash App | 107M transactions |
| C0772 | Afterpay | 72M transactions |

### Invoice Tracking
- Three pending invoices totaling **$2,959,249.50** (amendment prepayment)
- Invoice-level detail: amounts, PO numbers, due dates, payment status
- Links to Zip procurement requests for each invoice

### Usage & Forecasting
- Monthly transaction volumes by entity (Apr 2025 – Mar 2026)
- Utilization tracking against contracted allocations
- Overage projections through end of agreement (Sep 2026)
- Risk flags: Square and Cash App projected to exceed allocations

### Dashboards & Documents
- **TMX Invoice Dashboard:** [blockcell.sqprod.co/sites/tmx-invoice-dashboard/](https://blockcell.sqprod.co/sites/tmx-invoice-dashboard/)
- **Trust Vendor Dashboard (TMX page):** [blockcell.sqprod.co/sites/trust-vendor-dashboard/#detail-tmx](https://blockcell.sqprod.co/sites/trust-vendor-dashboard/#detail-tmx)
- Links to all agreements, usage reports, and SPADE documents on Google Drive

## Usage

### Run as an interactive recipe
```bash
goose run --recipe https://github.com/alain-block/goose-recipes/tree/main/threatmetrix
```
This starts a Goose session pre-loaded with all TMX vendor knowledge. Ask questions like:
- *"What's the overage rate if we exceed our allocation?"*
- *"Show me the invoice details for the Cash App billgroup"*
- *"What's our projected usage through September?"*
- *"Who's the account manager at LexisNexis?"*

### Install as a persistent skill
```bash
mkdir -p ~/.config/goose/skills/threatmetrix
curl -sL https://raw.githubusercontent.com/alain-block/goose-recipes/main/threatmetrix/SKILL.md \
  -o ~/.config/goose/skills/threatmetrix/SKILL.md
```
Once installed, the skill is available in all Goose sessions — no need to specify the recipe each time.

## Keeping It Updated

The skill file should be updated when:
- **Monthly usage data** is refreshed (update the usage table and forecasts)
- **Invoices are paid** (update payment status and Zip request status)
- **Contract changes** occur (new amendments, renewals, or term extensions)
- **New Zip requests** are created or approved

To update, edit `SKILL.md` and push to this repo. Colleagues get the latest version next time they run the recipe or re-install the skill.

## Related Resources

| Resource | Link |
|---|---|
| Trust Vendor Dashboard Recipe | [trust-vendor-dashboard/](../trust-vendor-dashboard/) |
| Trust Vendor Dashboard (live) | [blockcell.sqprod.co/sites/trust-vendor-dashboard/](https://blockcell.sqprod.co/sites/trust-vendor-dashboard/) |
| TMX Invoice Dashboard (live) | [blockcell.sqprod.co/sites/tmx-invoice-dashboard/](https://blockcell.sqprod.co/sites/tmx-invoice-dashboard/) |

## Owner

**Alain Gendre** — alain@block.xyz
