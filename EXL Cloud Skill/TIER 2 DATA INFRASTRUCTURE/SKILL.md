---
name: exl-cloud-tier-2
description: "Router for EXL Cloud Tier 2 data preparation. Sub-skills: (3) Clean & Validate Data — cleaning/validating raw spreadsheet data; (4) Trial Balance Mapper — mapping TB accounts to P&L/BS categories; (5) Period Table Builder — building tbl_Period date axis with ACT/FCST flags. Reference by number e.g. 'use skill 3', 'run skill 4', 'skill 5'. Trigger phrases — skill 3: 'clean this data', 'fix formatting', 'remove duplicates'; skill 4: 'map my TB', 'COA mapping', 'map Xero TB'; skill 5: 'build tbl_Period', 'create date table', 'ACT FCST toggle'. Always views the correct Reference/ file before proceeding — never guesses."
---

# EXL Cloud Tier 2 — Data Preparation Router

Router for all data preparation tasks. Identify the task, load the correct `Reference/` file, follow its instructions. Never guess — always load.

---

## Sub-Skill Directory

| # | Skill | File | Use When |
|---|-------|------|----------|
| 3 | Clean & Validate Data | `Reference/SKILL 3 CLEAN AND VALIDATE DATA.md` | Cleaning raw data: whitespace, duplicates, date formats, text-to-number |
| 4 | Trial Balance Mapper | `Reference/SKILL 4 TRIAL BALANCE MAPPER.md` | Mapping TB accounts to standardised P&L and BS categories |
| 5 | Period Table Builder | `Reference/SKILL 5 PERIOD TABLE BUILDER.md` | Building tbl_Period date axis with ACT/FCST flags for time-series workbooks |

---

## Routing Rules

### Single-Skill Tasks

| Task | Load |
|------|------|
| "clean this data", "fix the formatting", "remove duplicates", "standardise dates", "trim whitespace", "numbers stored as text", "data quality check", "prep this data" | `Reference/SKILL 3 CLEAN AND VALIDATE DATA.md` |
| "map my TB", "map trial balance", "categorise accounts", "map Xero TB", "map MYOB accounts", "COA mapping", "map accounts to P&L and BS" | `Reference/SKILL 4 TRIAL BALANCE MAPPER.md` |
| "build tbl_Period", "set up periods", "create date table", "build time series", "ACT FCST toggle", "financial year dates", "monthly periods" | `Reference/SKILL 5 PERIOD TABLE BUILDER.md` |

### Multi-Skill Tasks (Load Order Matters)

| Task | Load Order |
|------|------------|
| Clean raw data then map to P&L/BS | `skill-3` → `skill-4` |
| Full data prep for a new workbook | `skill-3` → `skill-4` → `skill-5` |

---

## Decision Tree

```
What is the task?
│
├── Raw data needs cleaning / standardising?
│    └── VIEW: Reference/SKILL 3 CLEAN AND VALIDATE DATA.md
│         └── Also needs TB mapping after? → VIEW: Reference/SKILL 4 TRIAL BALANCE MAPPER.md
│
├── Mapping TB accounts to P&L / BS categories?
│    └── VIEW: Reference/SKILL 4 TRIAL BALANCE MAPPER.md
│         (if data is not yet clean, run skill-3 first)
│
├── Building the period/date axis for a time-series model?
│    └── VIEW: Reference/SKILL 5 PERIOD TABLE BUILDER.md
│
└── Full data prep from scratch?
     └── VIEW skill-3 → skill-4 → skill-5 in sequence
```

---

## Notes

- **Skill 3 must run before Skill 4** — TB Mapper assumes clean, consistently formatted source data; dirty data causes broken mappings
- Skill 5 (Period Table) is independent of Skills 3 and 4 — it can be built at any point, but is typically set up after data is clean
- Skill 4 produces an Account Mapping sheet; that sheet feeds the Tier 3 commentary skills and Tier 4 variance analysis (skill 12)
