# Goose Recipes

Shared Goose recipes and skills for the Trust & Identity team — vendor management, invoice tracking, and dashboard maintenance.

## Vendors

| Vendor | Description | Quick Start |
|---|---|---|
| [**Experian**](experian/) | Consumer credit data — agreements, billing, subscriber codes, PO tracking, entity mapping across Square Capital, SFS, and Afterpay | `goose run --recipe experian/recipe.yaml` |
| [**ThreatMetrix**](threatmetrix/) | LexisNexis device intelligence & fraud detection — contract details, invoice reconciliation, usage tracking | `goose run --recipe threatmetrix/recipe.yaml` |
| [**Trust Vendor Dashboard**](trust-vendor-dashboard/) | Static dashboard tracking risk & identity vendor spend, utilization, and BU allocation | `goose run --recipe trust-vendor-dashboard/recipe.yaml` |

## Usage

### Run a recipe directly from GitHub
```bash
goose run --recipe https://github.com/alain-block/goose-recipes/tree/main/<vendor>
```

### Clone and run locally
```bash
git clone https://github.com/alain-block/goose-recipes.git
goose run --recipe goose-recipes/<vendor>/recipe.yaml
```

### Install a skill for auto-discovery
```bash
mkdir -p ~/.agents/skills/<vendor>
cp goose-recipes/<vendor>/SKILL.md ~/.agents/skills/<vendor>/SKILL.md
```

Then Goose will automatically load the skill when your questions match its domain.

## Structure

Each vendor folder follows the same pattern:

```
<vendor>/
├── SKILL.md        # Knowledge base — contacts, contracts, pricing, sub codes, etc.
├── recipe.yaml     # Goose recipe that loads the skill and provides assistance
└── README.md       # Usage instructions and topic overview
```
