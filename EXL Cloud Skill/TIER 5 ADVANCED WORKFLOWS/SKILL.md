---
name: exl-cloud-tier-5
description: "Router for EXL Cloud Tier 5 advanced workflows — end-to-end, workbook-specific builds and reviews. Sub-skills: (16) Weekly Cash Flow Scenario Overlay Builder; (17) Human Capital Report Commentary; (18) Invoice & Bill Pull Analysis; (19) Performance Report Dashboard Commentary; (20) Assumption & Output Reasonableness Check; (21) Model Best Practice Benchmark. Reference by number e.g. 'use skill 16', 'run skill 18', 'skill 21'. Trigger phrases cover skills 16–21. Always load the correct Reference file before proceeding — never guess."
---

# EXL Cloud Tier 5 — Advanced Workflows Router

**Router for all Tier 5 tasks.** Identify the task, load the correct `Reference/` file, follow its instructions. Never guess — always load.

---

## Skill Directory

| # | Skill | File | Use When |
|---|-------|------|----------|
| 16 | Weekly Cash Flow Scenario Overlay Builder | `Reference/SKILL 16 WEEKLY CASH FLOW SCENARIO OVERLAY BUILDER.md` | Building and populating Base/Best/Worst scenarios in an EXL Cloud Weekly CF model |
| 17 | Human Capital Report Commentary | `Reference/SKILL 17 HUMAN CAPITAL REPORT COMMENTARY.md` | Building an HC executive summary from payroll data in an EXL Cloud model |
| 18 | Invoice & Bill Pull Analysis | `Reference/SKILL 18 INVOICE BILL PULL ANALYSIS.md` | Analysing Xero invoice/credit note data pull workbooks (4-sheet suite) |
| 19 | Performance Report Dashboard Commentary | `Reference/SKILL 19 PERFORMANCE REPORT COMMENTARY.md` | Writing dashboard commentary for EXL Cloud Performance Reports workbooks |
| 20 | Assumption & Output Reasonableness Check | `Reference/SKILL 20 ASSUMPTION AND OUTPUT REASONABLENESS CHECK.md` | Sanity-checking whether model assumptions and the outputs they produce sit in reasonable ranges |
| 21 | Model Best Practice Benchmark | `Reference/SKILL 21 MODEL BEST PRACTICE BENCHMARK.md` | Scoring any Excel model against best-practice criteria with a findings log and a phased remediation plan (hours only) |

---

## Routing Rules

| Task | Load |
|------|------|
| "populate the weekly cash flow scenarios", "build CF scenarios", "set up the scenario overlay", "weekly cash flow analysis", "populate Base/Best/Worst", "scenario overlay for the weekly model", "add scenarios to the weekly CF" | `Reference/SKILL 16 WEEKLY CASH FLOW SCENARIO OVERLAY BUILDER.md` |
| "build HC analysis", "human capital report", "payroll executive summary", "workforce commentary", "HC commentary", "salary data analysis", "headcount analysis", "workforce snapshot", "staffing cost summary", "build the HC exec summary", "analyse the payroll data" | `Reference/SKILL 17 HUMAN CAPITAL REPORT COMMENTARY.md` |
| "analyse the invoice pull", "build the bill analysis", "invoice and credit note analysis", "what does this Xero pull show", "build the exec summary for this data", "analyse the billing data", "invoice pull dashboard", "break down the invoices" | `Reference/SKILL 18 INVOICE BILL PULL ANALYSIS.md` |
| "write dashboard commentary", "populate the commentary text boxes", "fill in the dashboard text boxes", "write CF4 commentary for the dashboards", "update the actuals/revenue/expense/EBITDA/combo dashboard commentary", "analyse the P&L and write commentary" | `Reference/SKILL 19 PERFORMANCE REPORT COMMENTARY.md` |
| "reasonableness check", "are these assumptions sensible", "sanity-check the model", "flag anything wildly out of range", "does the output make sense", "check the growth rates", "is this forecast plausible", "does the worst case actually stress cash" | `Reference/SKILL 20 ASSUMPTION AND OUTPUT REASONABLENESS CHECK.md` |
| "run the benchmark", "benchmark this model", "model health check", "how well is this model built", "build the evidence map", "score this model against best practice" | `Reference/SKILL 21 MODEL BEST PRACTICE BENCHMARK.md` |

---

## Decision Tree

```
What is the task?
│
├── Weekly Cash Flow Scenario Model needing Base/Best/Worst scenarios?
│    └── LOAD: Reference/SKILL 16 WEEKLY CASH FLOW SCENARIO OVERLAY BUILDER.md
│
├── EXL Cloud financial model needing Human Capital / Payroll analysis?
│    └── LOAD: Reference/SKILL 17 HUMAN CAPITAL REPORT COMMENTARY.md
│
├── Xero invoice/credit note data pull needing analysis & dashboarding?
│    └── LOAD: Reference/SKILL 18 INVOICE BILL PULL ANALYSIS.md
│
├── EXL Cloud Performance Reports workbook needing dashboard commentary?
│    └── LOAD: Reference/SKILL 19 PERFORMANCE REPORT COMMENTARY.md
│
├── Sense-check whether assumptions and/or outputs are plausible?
│    └── LOAD: Reference/SKILL 20 ASSUMPTION AND OUTPUT REASONABLENESS CHECK.md
│         └── Want every input catalogued instead? → use Tier 4 skill-9
│         └── Want formula / BS balance errors instead? → use Tier 4 skill-10
│
└── Score how well a model is built against best practice (health check)?
     └── LOAD: Reference/SKILL 21 MODEL BEST PRACTICE BENCHMARK.md
          └── Requires Tier 1 (skills 1 and 2) for formatting and the final standards check
          └── Want only a plausibility check of the numbers? → use skill-20
```

---

## Multi-Skill Tasks & Load Order

| Task | Load Order |
|------|------------|
| Weekly CF scenarios + executive summary | `skill-16` (auto-builds Exec Summary) → Feeds into Tier 3 for commentary if needed |
| HC analysis standalone | `skill-17` (standalone) → Feeds into Tier 3 skill-7 (board paper) if needed |
| Invoice analysis standalone | `skill-18` (4-sheet suite) → Can feed into Tier 3 for narrative if needed |
| Dashboard commentary only | `skill-19` (text boxes only) → Works within Performance Reports workbook |
| Reasonableness review of a finished model | `skill-20` (assumptions + outputs in one sheet) → Fix flagged drivers, then re-run `skill-20` to refresh against unchanged bounds |
| Full model review package | Tier 4 `skill-10` (integrity) → `skill-20` (plausibility) → Tier 4 `skill-9` (full input register, if a complete catalogue is also needed) |
| Model health check / benchmark | Tier 1 `skill-1` + `skill-2` (standards, loaded by skill 21) → `skill-21` (score, findings, remediation plan) → delete the BM tabs and `BM_` names before the file returns to the client |

---

## Notes

- **Skills 16–19**: Domain and workbook-specific — each tied to a particular workbook structure or data source, and each runs independently of the others
- **Skill 20**: A model review workflow for EXL Cloud / Model Citizn 3-way workbooks — pairs with Tier 4 skills 9 and 10
- **Skill 21**: A build-quality health check for any Excel model — scores against best-practice criteria, logs findings and builds an hours-only remediation plan. It reports problems; it does not fix them
- **Cross-tier flow**: Tier 5 outputs (especially skill 16) feed into Tier 3 for narrative assembly
- **Skills 9, 10 (Tier 4) and 20 are distinct, not overlapping**: 9 catalogues *every* input, 10 tests arithmetic and structural *correctness*, 20 tests whether the numbers are *plausible*. A model can pass 10 completely and still fail 20
- **Skills 20 and 21 are distinct**: 20 tests whether the *numbers* are plausible; 21 tests how well the *model is built*
