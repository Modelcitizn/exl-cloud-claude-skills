---
name: exl-cloud-tier-4
description: "Router for EXL Cloud Tier 4 augmentation skills — governance, analysis, and delivery add-ons that apply to any workbook. Sub-skills: (9) Assumption Register Builder; (10) Anomaly Detection; (11) Model Documentation Generator; (12) Variance Analysis Reporter; (13) KPI Dashboard Formatter; (14) Change Log & Audit Trail; (15) Meeting Prep Pack. Reference by number e.g. 'use skill 9', 'run skill 12', 'skill 15'. Trigger phrases cover skills 9–15. Always load the correct Reference file before proceeding — never guess."
---

# EXL Cloud Tier 4 — Augmentation Router

**Router for all Tier 4 tasks.** Identify the task, load the correct `Reference/` file, follow its instructions. Never guess — always load.

---

## Skill Directory

| # | Skill | File | Use When |
|---|-------|------|----------|
| 9 | Assumption Register Builder | `Reference/SKILL 9 ASSUMPTION REGISTER BUILDER.md` | Extracting all model inputs and hardcoded values into a structured register |
| 10 | Anomaly Detection | `Reference/SKILL 10 ANOMALY DETECTION SKILL.md` | Adding a Validation Checks sheet with PASS/FAIL checks and conditional formatting |
| 11 | Model Documentation Generator | `Reference/SKILL 11 MODEL DOCUMENTATION GENERATOR.md` | Creating a structured Instructions sheet for any workbook |
| 12 | Variance Analysis Reporter | `Reference/SKILL 12 VARIANCE ANALYSIS REPORTER.md` | Building actuals vs budget/forecast/prior comparison with $ and % variances |
| 13 | KPI Dashboard Formatter | `Reference/SKILL 13 KPI DASHBOARD FORMATTER.md` | Building a one-page KPI tile dashboard with traffic lights |
| 14 | Change Log & Audit Trail | `Reference/SKILL 14 CHANGE LOG AND AUDIT TRAIL.md` | Adding Change Log and Claude Log governance sheets |
| 15 | Meeting Prep Pack | `Reference/SKILL 15 MEETING PREP PACK.md` | Generating a concise brief for board/committee meeting presentation |

---

## Routing Rules

| Task | Load |
|------|------|
| "extract assumptions", "build assumption register", "list all inputs", "find hardcoded values", "assumption audit", "document the inputs" | `Reference/SKILL 9 ASSUMPTION REGISTER BUILDER.md` |
| "add validation checks", "build checks", "add error detection", "PASS FAIL checks", "model integrity checks", "add balance sheet check", "audit checks" | `Reference/SKILL 10 ANOMALY DETECTION SKILL.md` |
| "document this model", "add instructions", "create documentation", "explain the model structure", "add a readme sheet", "document the workbook" | `Reference/SKILL 11 MODEL DOCUMENTATION GENERATOR.md` |
| "variance analysis", "actual vs budget", "compare to forecast", "what changed vs last year", "variance report", "budget variance", "explain the variances" | `Reference/SKILL 12 VARIANCE ANALYSIS REPORTER.md` |
| "build a dashboard", "KPI tiles", "executive dashboard", "one-page summary", "traffic light dashboard", "key metrics view", "management dashboard" | `Reference/SKILL 13 KPI DASHBOARD FORMATTER.md` |
| "add change log", "add audit trail", "track changes", "version history", "add governance sheets", "claude log", "AI audit trail", "model governance" | `Reference/SKILL 14 CHANGE LOG AND AUDIT TRAIL.md` |
| "meeting prep", "prepare talking points", "board meeting brief", "committee prep", "what should I present", "meeting pack" | `Reference/SKILL 15 MEETING PREP PACK.md` |

---

## Decision Tree

```
What is the task?
│
├── Extract / document model assumptions or hardcodes?
│    └── LOAD: Reference/SKILL 9 ASSUMPTION REGISTER BUILDER.md
│         └── Want a plausibility check instead? → hand off to Tier 5 skill-20
│
├── Add validation checks or PASS/FAIL integrity sheet?
│    └── LOAD: Reference/SKILL 10 ANOMALY DETECTION SKILL.md
│
├── Add an Instructions / documentation sheet to the workbook?
│    └── LOAD: Reference/SKILL 11 MODEL DOCUMENTATION GENERATOR.md
│
├── Compare actuals to budget, forecast, or prior period?
│    └── LOAD: Reference/SKILL 12 VARIANCE ANALYSIS REPORTER.md
│         └── Need commentary too? → hand off to Tier 3 skill-6
│
├── Build a KPI dashboard with traffic lights?
│    └── LOAD: Reference/SKILL 13 KPI DASHBOARD FORMATTER.md
│         └── Feeding into board paper? → hand off to Tier 3 skill-7
│
├── Add change tracking / audit trail / Claude log?
│    └── LOAD: Reference/SKILL 14 CHANGE LOG AND AUDIT TRAIL.md
│
└── Prepare for a board or committee meeting?
     └── LOAD: Reference/SKILL 15 MEETING PREP PACK.md
```

---

## Multi-Skill Tasks & Load Order

| Task | Load Order |
|------|------------|
| Variance analysis + commentary feed | `skill-12` → (hand off to Tier 3 skill-6) |
| KPI dashboard + board paper feed | `skill-13` → (hand off to Tier 3 skill-7) |
| Full governance package | `skill-9` → `skill-10` → `skill-11` → `skill-14` |
| Full delivery package (analysis + dashboard + meeting prep) | `skill-12` → `skill-13` → `skill-15` |
| Full model review package | `skill-10` (integrity) → Tier 5 `skill-20` (plausibility) → `skill-9` (full input register, if a complete catalogue is also needed) |

---

## Notes

- **Skills 9–15**: Generic governance, analysis, and delivery tasks — applicable to any workbook type
- **Workbook-specific and model review skills (16–21)** live in Tier 5 — Advanced Workflows
- **Recommended governance sequence**: 9 → 10 → 11 → 14 for a full governance package
- **Cross-tier flow**: Tier 4 outputs (especially skills 12, 13) feed into Tier 3 for narrative assembly
- **Skill Independence**: Skills 9, 10, 11, 14 can run in any order
- **Skills 9, 10 and Tier 5 skill 20 are distinct, not overlapping**: 9 catalogues *every* input, 10 tests arithmetic and structural *correctness*, 20 tests whether the numbers are *plausible*. A model can pass 10 completely and still fail 20
