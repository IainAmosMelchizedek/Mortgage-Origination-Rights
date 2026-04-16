# Consumer Financial Rights: Mortgage Origination Disclosures
### An Apify Actor by Safe Passage Strategies LLC

---

> *"Ignorance truly is a product. That ends here."*

---

## What This Is

This Actor extracts the complete federal examination standard for mortgage
origination — all eight modules of the CFPB Mortgage Origination Examination
Procedures — and structures it into a clean, queryable dataset of 104 unique
rows covering every loan product type, disclosure requirement, consumer right,
CFR citation, and examiner checkpoint in the document.

Federal examiners walked into mortgage lenders and brokers with this document
in hand. They knew exactly what was required. Consumers sitting across the
closing table did not.

This Actor changes that.

---

## What It Extracts

The dataset covers the full scope of federal mortgage origination examination
procedures across eight modules:

| Module | Coverage |
|---|---|
| Module 1: Company Business Model | Loan product types, origination channels, mini-correspondent requirements, RESPA kickback prohibitions |
| Module 2: Advertising and Marketing | All 19 MAP Rule prohibited misrepresentation categories (12 CFR 1014.3(a)-(s)), CHARM Booklet, FCRA affiliate marketing |
| Module 3: Loan Originators | Compensation rules, prohibited payments, dual compensation, steering prohibitions, SAFE Act licensing, NMLSR requirements |
| Module 4: Loan Disclosures - Closed-End | Loan Estimate, Closing Disclosure, TRID requirements, timing rules, fee tolerances, right of rescission notices |
| Module 5: Loan Disclosures - Other | GFE, HUD-1, TILA disclosures, HELOCs, right to rescind, PMI termination, escrow statements, rate locks |
| Module 6: Appraisals | Written appraisal requirements, two-appraisal rule, consumer copy rights, valuation independence |
| Module 7: Underwriting | Ability-to-repay, qualified mortgage standards, high-cost mortgages, higher-priced mortgage loans, HELOC requirements |
| Module 8: Examiner Conclusions | Report of examination, MRA requirements, compliance management system |

---

## Dataset Schema

Each row in the dataset represents one mortgage product, disclosure,
right, or protection and contains the following fields:

| Field | Type | Description |
|---|---|---|
| `product_name` | String | Exact name of the product, disclosure, or right |
| `product_definition` | String | Complete plain language definition — never empty |
| `product_category` | String | One of 14 categories including MAP Rule Prohibition, Disclosure, Appraisal, Underwriting Standard, etc. |
| `module` | String | Which examination module this row belongs to |
| `authorization_required` | Boolean | Whether regulatory authorization is required |
| `disclosure_required` | Boolean | Whether written consumer disclosure is required |
| `cancellation_rights` | Boolean | Whether consumer holds rescission or cancellation rights |
| `cancellation_and_refund_rights` | String | Specific conditions (where applicable) |
| `examiner_checkpoints` | Array | Exact procedural steps federal examiners follow |
| `authorization_requirements` | Array | Specific requirements with CFR citations |
| `disclosure_requirements` | Array | Specific requirements with CFR citations |
| `source_document` | String | Source federal documents |
| `extraction_date` | Date | Date extracted |

---

## Dataset Statistics

| Metric | Value |
|---|---|
| Total unique rows | 104 |
| Modules covered | 8 of 8 |
| MAP Rule prohibition rows | 19 (one per 12 CFR 1014.3 category) |
| Rows with CFR citations | 99 of 104 (96%) |
| Empty definitions | 0 |
| Empty examiner checkpoints | 0 |
| Duplicate product names | 0 |

---

## Knowledge Sources

| Source | Type |
|---|---|
| CFPB Mortgage Origination Examination Procedures (December 2021) | PDF — 49 pages, 540,935 bytes |
| CFPB Supervisory Highlights Issue 33 Spring 2024 | PDF — 250,495 bytes |
| CFPB Mortgage Origination Procedures webpage | Web scrape |
| CFPB Supervisory Highlights Issue 33 webpage | Web scrape |

---

## Technical Architecture

- **Runtime:** Node.js v22
- **Actor framework:** Apify SDK v3.7.0
- **Web scraping:** Crawlee CheerioCrawler v3.16.0
- **PDF parsing:** pdfjs-dist v5.6.205 (legacy build)
- **Intelligence extraction:** Anthropic Claude claude-opus-4-5
- **API calls per run:** 8 (one per module)
- **Max tokens per call:** 8,000
- **Average run time:** ~288 seconds (~4.8 minutes)

---

## Input

| Field | Type | Required | Description |
|---|---|---|---|
| `ANTHROPIC_API_KEY` | String (secret) | Yes | Your Anthropic API key for Claude analysis |

Set this in your `.env` file:

```
ANTHROPIC_API_KEY=your_key_here
```

---

## Output

The Actor produces two outputs per run:

**1. Apify Dataset**
104 structured rows pushed to the default dataset, accessible via
Apify API or the Apify console.

**2. Key-Value Store: RAW_RESULTS**
A JSON object containing:
- `extractionDate` — date of this run
- `totalRows` — count of unique rows
- `modulesCovered` — array of module names found
- `webPagesScraped` — array of URLs scraped
- `rows` — complete array of all dataset rows

---

## Usage Example

```javascript
// Fetch the dataset via Apify API
const response = await fetch(
  `https://api.apify.com/v2/datasets/{DATASET_ID}/items?token={API_TOKEN}`
);
const rows = await response.json();

// Find all rows related to the right of rescission
const rescissionRights = rows.filter(row =>
  row.product_name.toLowerCase().includes('rescind') ||
  row.product_definition.toLowerCase().includes('rescission')
);

// Find all MAP Rule prohibition rows
const mapRuleRows = rows.filter(row =>
  row.product_category === 'MAP Rule Prohibition'
);

// Find all rows with cancellation rights
const cancellationRows = rows.filter(row =>
  row.cancellation_rights === true
);
```

---

## Consumer-Facing Application

This dataset powers **Was Your Closing Fair?** — a situation-driven
consumer tool built on this dataset:

**Live:** https://mortgage.safepassagestrategies.com

Consumers select the problem they encountered:
- Fees I don't understand at closing
- My loan terms changed before closing
- I was denied and want to know why
- My rate is different from what I was quoted
- I want to understand my right of rescission

The tool queries this dataset and returns exactly what federal law
required the lender to do, the consumer's specific rights, and what
a federal examiner would investigate in that situation.

---

## Part of the Consumer Financial Rights Catalog

This is the third Actor in a planned series covering major consumer
financial product verticals:

| Actor | Status |
|---|---|
| Auto Loan Optional Products | ✅ Complete |
| Credit Card Account Management | ✅ Complete |
| Mortgage Origination Disclosures | ✅ Complete |
| Debt Collection Consumer Rights | 🔜 Planned |
| Education Loan Servicing | 🔜 Planned |
| Short-Term Small-Dollar Lending | 🔜 Planned |

---

## Legal Disclaimer

This dataset contains factual information extracted from publicly
available federal government examination procedures. It is educational
in nature. It is not legal advice. Nothing in this dataset or any
application built on it should be construed as legal counsel or as a
substitute for qualified legal representation.

---

## Built By

**Safe Passage Strategies LLC**
https://safepassagestrategies.com
iain@safe-passage-strategies.com

Iain Melchizedek — 17 years of federal government experience including
the Consumer Financial Protection Bureau, where he conducted compliance
examinations of depository and non-depository financial institutions.
