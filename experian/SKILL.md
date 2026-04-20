# Experian Vendor Relationship — Block

> **Last updated:** 2026-04-13
>
> **Source:** Internal sync — Experian — agreement, PO, cost center, invoices (2026-04-07); Clerk Vendor Info (Notion)

---

## 1. Relationship Overview

Block maintains a **complex, multi-layered relationship** with Experian, driven primarily by the **regulated nature of consumer credit data**. Multiple Block subsidiaries consume Experian data under distinct legal agreements, each with its own billing and purchase-order requirements.

---

## 2. Agreement Structure

### Master Service Agreement (MSA)
- Exists at the **Block (parent)** level.
- Provides the foundational contractual framework for *all* Experian engagements across subsidiaries.

### Entity-Specific Agreements
Each subsidiary holds its **own agreement** under the MSA because **the legal entity receiving and using the credit data must be the one charged**:

| Entity | Agreement Type | Notes |
|---|---|---|
| **Square Financial Services (SFS)** | Own agreement under MSA | Linked to Block via an "agreement to be bound" |
| **Square Capital** | Own agreement under MSA | Listed as an affiliate of Block; enables data sharing |
| **Afterpay** | Own agreement under MSA | Included in bundled pricing; limited internal visibility |

### Why Separate Agreements?
- Consumer credit data is **highly regulated**.
- The legal entity that *receives* the data must be the entity that is *billed*.
- Regulatory requirements (especially SFS's bank charter) prevent consolidation into a single agreement or PO.

---

## 3. Billing & Pricing

### Bundled Pricing
- Implemented **~2 years ago** (circa 2024) to replace inefficient volume-driven pricing.
- Under the old model, Square was paying **up to 4× what Afterpay paid** for equivalent services.
- The bundled structure is **primarily driven by Afterpay's substantial volume**, which gives the overall relationship better pricing leverage.

### Per-Entity Billing
Despite the bundled pricing umbrella, **invoicing is still conducted per entity**, based on the specific use case.

- **Frequency:** Monthly
- **Timing:** Invoices arrive **~30 days after the billing period closes**
- ⚠️ Internal PO processing delays sometimes cause **multiple invoices to hit at once**

### Subscriber Codes (Sub Codes)
- Every Experian invoice carries a **subscriber code (sub code)**.
- Each sub code maps to a **specific legal entity + use case**.
- Sub codes are listed in the production tables of the internal mapping document.
- **Key use:** Tracking and allocating costs to the correct business unit.

---

## 4. Purchase Order (PO) Structure

### Guiding Principle
> POs are created **per entity**, not per use case — driven by regulatory constraints.

SFS **must pay its own invoices** due to its bank charter status, making a single consolidated PO across Square entities impossible.

### Active POs (Square Side)

| PO Entity | Use Case(s) Covered |
|---|---|
| **Square Financial Services (SFS)** | Square Loans |
| **Square Capital** | Credit Card |
| **Square Capital** | Invitation to Apply (pre-qualification flow) |
| **Square Capital** | Hardware Financing |
| **Square Capital** (budget) | Archived data purchases (~$200K per instance, ~2×/year) |

### PO Challenges
- **Tracking individual use-case pacing** is difficult when multiple Square Capital use cases are combined into one PO — requires reviewing each invoice and mapping the sub code.
- **Forecasting** is historically difficult: roadmap changes cause aggressive volume ramps, leading to multiple PO increases or additional budget requests.
- **Oracle** can pull vendor invoices but **lacks PO linkage visibility** without manually downloading each invoice and checking the PO number.

---

## 5. Entity Roles Summary

| Entity | Role |
|---|---|
| **Block** | Parent entity; holds the MSA with Experian |
| **Square Financial Services (SFS)** | Regulated bank entity; handles core **Square Loans** |
| **Square Capital** | Historical lending entity (pre-bank); handles **credit card, hardware financing, pre-qualification flow** |
| **Afterpay** | Included in bundled pricing; drives volume leverage; limited internal visibility into their Experian operations |

### Data Sharing
- SFS is bound to Block through an **"agreement to be bound."**
- Square Capital is listed as an **affiliate of Block**, allowing data sharing among the entities.

---

## 6. Future Strategy

### Consolidation Under SFS (Long-Term)
- **Goal:** Move all products into Square Financial Services.
- **Blocker:** Requires **FDIC approval** for each new product added to the bank (e.g., credit card).
- This is a gradual process, not an imminent change.

### 2026 — Access Loans (Potential New Use Case)
- **What:** A short-term loan product for sellers who **do not qualify** for the flagship Square Loans product.
- **Experian impact:** May require obtaining a **new credit consent** from Experian.

### Longer-Term — Single Credit Consent at Square Onboarding
- **What:** Place a single credit consent at the point of Square onboarding to enable better **pre-packaging and targeting of offers**.
- **Status:** Discussed for **18+ months**, but delayed due to regulatory and roadmap factors.
- **Timeline:** Likely a discussion for 2027+.

---

## 7. Key Challenges & Pain Points

| Challenge | Detail |
|---|---|
| **Forecasting** | Roadmap changes cause aggressive volume ramps → multiple PO increases / budget requests |
| **Scattered PO process** | Historically, issuing POs required gathering scattered information across teams |
| **Invoice-to-PO tracking** | Oracle lacks PO linkage visibility; requires manual invoice download to check PO numbers |
| **Use-case pacing** | Hard to track when multiple use cases share a single Square Capital PO |
| **Invoice timing** | PO processing delays can cause invoice batching |
| **Afterpay visibility** | Limited knowledge of Afterpay's internal Experian operations and billing |

---

## 8. Experian Contacts (Vendor Side)

### Experian CIS (Consumer Information Services)

| Role | Contact |
|---|---|
| **Primary Contact (Client Executive)** | Michael.CKennedy@experian.com · +1 (347)-262-9689 (m) |
| **Technical Contacts** | lynne.cadena@experian.com · luke.wain@experian.com |
| **Production Support — Connectivity Outages (EGOC)** | 800-553-4785 · EGOC-US@experian.com |
| **Technical Issues / Password Resets / Account Access** | 800-854-7201 |
| **Customer Service Related Questions** | 800-831-5614 |
| **Data Development / Bulleseye Reports** | 800-553-4785 |
| **API Hub Assistance — Technical Support** | 800-854-7201 · TSCAPISupport@experian.com · apihub-support@experian.com |

### Experian BIS (Business Information Services)

| Role | Contact |
|---|---|
| **Primary Contact (Client Executive)** | Michael.CKennedy@experian.com · +1 (347)-262-9689 (m) |
| **Technical Contacts** | lynne.cadena@experian.com · luke.wain@experian.com |
| **Production Support** | TSCBISSupport@experian.com |

---

## 9. Account Info & Dashboard Access

### Experian Dashboard
- **Primary:** https://origin-na.experian.com/dashboard
- **Alternate (Okta):** https://experian-nab.okta.com/

### Block Accounts on Experian

| Account | Account Role | Email | Company ID |
|---|---|---|---|
| **SQ_CAPITAL** | End User | credit-underwriting@squareup.com | 621068 |
| **SQ_SFS** | Security Designate | credit-underwriting@squareup.com | 861984 |
| **KEISHAWNJ** | Security Designate | keishawn@squareup.com | 621068 |
| **[suspended] AUDREYJKIM** | Security Designate | audrey@squareup.com | 621068 |

---

## 10. Subscriber Codes (Sub Codes) — Use Case Mapping

> Sub codes are the key to mapping Experian invoices → use cases → Block entities. Passwords are stored in Clerk secrets and auto-updated every 30 days via Clerk.

### Full Sub Code Table

| Use Case | UserID | Sub-Code(s) | Inquiry Type | Eligible Experian Products | Company ID | Block Entity |
|---|---|---|---|---|---|---|
| **Hardware Financing** | sq_capital | 2663970 (pre-qual) · 2669665 (underwriting) | Soft-Inquiry (Pre-qual) · Hard Inquiry (Underwriting) | Credit Report (FICO9), PreciseID (Identity Verification), Verification of Income/Assets | 621068 | Square Capital |
| **Apply for Capital (AFC)** | sq_capital | 2910853 | Soft-Inquiry | — | — | Square Capital |
| **Consumer Installments** | sq_capital | 2663970 (pre-qual) · 2914596 (underwriting) | Soft-Inquiry (Pre-qual) · Hard-Inquiry (Underwriting) | — | — | Square Capital |
| **Term Loans (BIS)** | sq_capital | 619600 | Soft-Inquiry (Pre-qual) | Credit Report (FICO9) | — | Square Financial Services |
| **Term Loans (CIS)** | sq_financial_services | 3101569 | Soft-Inquiry (Pre-qual) | Credit Report: FICO9, Vantage4, Premier Attributes v1.3 | — | Square Financial Services |
| **US Flex** | sq_financial_services | 3101599 | Soft-Inquiry (Written Consent) | Credit Report: FICO9, Vantage4, Premier Attributes v1.3 | — | Square Financial Services |
| **Square Credit Card** | sq_capital | 3778507 | Soft-Inquiry (Written Consent) | Credit Report: FICO9, Vantage4, Premier Attributes v1.3 | — | Square Capital |
| **Square Credit Card: ITA** | sq_capital | 3976603 (pre-qual, PP Code: 3F) · 2914596 (underwriting, PP Code: 98) | Soft-Inquiry (Pre-qual) · Hard Inquiry (Underwriting) | Credit Report: FICO9, Vantage4, Premier Attributes v1.3 | — | Square Capital |

### Premier Attributes (Common Across CIS Sub Codes)
The following Premier Attributes are currently pulled for **Term Loans (CIS), US Flex, Square Credit Card, and Square Credit Card: ITA**:

```
P13_ALL5935, P13_ALL7347, P13_ALL7936, P13_ALL8152, P13_ALX8220,
P13_BCA7212, P13_BCA8110, P13_BCC7117, P13_BCC7708, P13_CRU8320,
P13_IQT9420, P13_PIL8220, P13_REH7120, P13_RTA7300, P13_RTR0300,
P13_RTR3422, P13_BCC7110, P13_MTA5830
```

### 🆕 Pending: New Premier Attributes Request (April 2026)

**Status:** Requested on 2026-04-17 by Keishawn Johnson → confirmed receipt by Michael Kennedy (Experian). Pending updated Project Brief and SoW.

**14 additional attributes** requested to be added to the following sub codes:

| Sub Code | UserID | Use Case |
|---|---|---|
| 3976603 | sq_capital | Square Credit Card: ITA (pre-qual) |
| 2914596 | sq_capital | Consumer Installments / Credit Card: ITA (underwriting) |
| 3778507 | sq_capital | Square Credit Card |
| 3101599 | sq_financial_services | US Flex |

**New attributes to be added:**
```
P13_ALL4028, P13_ALL6230, P13_ALL6250, P13_ALL7120, P13_ALL7332,
P13_ALL7358, P13_ALM6160, P13_AUA8320, P13_BCX3423, P13_BRC8320,
P13_ILN5422, P13_IQZ9427, P13_MTF8220, P13_RTR7150
```

**Notes:**
- Pricing is already covered under the Block bundled pricing agreement (shared with Afterpay).
- Requires an updated Project Brief to confirm the complete list of attributes and SoW.
- Keishawn Johnson is on parental leave starting week of 2026-04-21. **Sammy Salustri**, **Alain Gendre**, and **Kevin Suehnholz** are covering in his absence.
- Request originated from the ML team.

### Notes
- **Term Loans (BIS)** — BIS integration is **no longer used**; CIS is used for Term Loans in production.
- **Hardware Financing** — Associated email: winnie@squareup.com
- **All sub codes** — Passwords stored in Clerk secrets, auto-updated every 30 days via Clerk.
- **Consumer Installments** shares sub code **2663970** (pre-qual) with **Hardware Financing**.
- **Square Credit Card: ITA** shares sub code **2914596** (underwriting) with **Consumer Installments**.

### Staging Use-Cases (Non-Production)

| Use Case | UserID | Sub-Code(s) | Associated Email |
|---|---|---|---|
| **Hardware Financing** | squareup_demo | 2663970 (soft-inquiry) | audrey@squareup.com |
| **Apply for Capital (AFC)** | squareup_demo | 2910853 (soft-inquiry) | — |
| **Consumer Installments** | squareup_demo | 2663970 (pre-qual) · 2914596 (underwriting) | — |
| **Term Loans (BIS)** | squareup_bisdemo | — | credit-experian@squareup.com |
| **Term Loans (CIS)** | squarefinancialdemo | 3101569 (pre-qual) | — |

---

## 11. Internal Contacts (Block Side)

### People
- **Keishawn Johnson** — Product Manager; shared bundled pricing agreement link and vendor info; Security Designate on Experian. On parental leave starting week of 2026-04-21. keishawn@squareup.com · 973-820-3524
- **Alain Gendre** — Leading PO structure documentation; mapping use cases, sub codes, and invoices. alain@squareup.com
- **Sammy Salustri** — Covering for Keishawn during parental leave. ssalustri@block.xyz
- **Kevin Suehnholz** — Covering for Keishawn during parental leave. kevinsue@squareup.com
- **Allen Lu** — Exploring Oracle/Tradeshift invoice visibility improvements (via Goose/Claude)

### Documents
- **Clerk Vendor Info** — [Notion](https://www.notion.so/square-seller/Clerk-Vendor-Info-658f49b9590d427ebe6e8482e9fa9e09)
- **Experian Agreement Documentation** — Google Doc (shared by Keishawn Johnson)
- **Internal Mapping Document** — Contains production tables mapping sub codes → entity + use case

---

## 12. Quick Reference: How to Work with Experian Invoices

1. **Identify the sub code** on the invoice.
2. **Look up the sub code** in the sub code table (Section 10 above).
3. **Map to entity + use case** (e.g., sub code 3101569 → SFS → Term Loans).
4. **Match to the correct PO** based on the entity (SFS PO or Square Capital PO).
5. **Allocate cost** to the appropriate business unit / cost center.

---

*This skill file is a living document. Update it as agreements change, new use cases are added, or the entity structure evolves.*
