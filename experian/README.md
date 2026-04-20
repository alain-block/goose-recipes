# Experian Vendor Relationship

Goose skill and recipe for managing Block's relationship with Experian — the primary provider of consumer credit data used across Square Capital, Square Financial Services, and Afterpay.

## What's Included

- **SKILL.md** — Comprehensive knowledge base covering agreements, billing, subscriber codes, PO structure, contacts, entity mapping, and future strategy
- **recipe.yaml** — Goose recipe that loads the skill and provides vendor management assistance

## Usage

### As a Recipe
```bash
goose run --recipe experian/recipe.yaml
```

### As a Skill
Copy `SKILL.md` to your local skills directory:
```bash
mkdir -p ~/.agents/skills/experian-vendor
cp SKILL.md ~/.agents/skills/experian-vendor/SKILL.md
```

Then in any Goose session, ask Experian-related questions and the skill will auto-load.

## Key Topics Covered

- Agreement structure (MSA → entity-specific agreements)
- Subscriber codes → entity → use case mapping
- Purchase order (PO) tracking
- Bundled pricing (driven by Afterpay volume)
- Experian contacts (CIS & BIS)
- Block internal contacts
- Future strategy (SFS consolidation, Access Loans)
- Premier Attributes (current + pending April 2026 request)
