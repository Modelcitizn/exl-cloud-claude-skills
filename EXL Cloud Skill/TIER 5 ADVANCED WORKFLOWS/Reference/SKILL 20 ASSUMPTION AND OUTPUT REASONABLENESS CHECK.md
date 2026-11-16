---
name: skill-20-exl-cloud-assumption-output-reasonableness-check
description: "Sanity-check a model's assumptions and outputs against reasonable ranges. Use when the user says 'reasonableness check', 'are these assumptions sensible', 'sanity-check the model inputs or outputs', 'flag anything wildly out of range', 'does the output make sense', 'run skill 20', or invokes /assumption-output-reasonableness-check. Builds a single 'Assumption & Output Reasonableness Check' sheet with one combined Summary and two separated sections — an assumption section (finds driver sheets by content, links Live Value to source, flags against editable bounds) and an output section (trend sanity, cash & liquidity, margin/ratio bands, Worst<=Base<=Best scenario ordering). Do NOT use for a full input register (use skill 9), formula or balance sheet error hunting (use skill 10), or narrative commentary (use skill 6)."
---

# Assumption & Output Reasonableness Check

Assumptions can each look sensible in isolation and still combine into an output that doesn't make sense — individually-fine growth rates that compound into a hockey-stick, a "Worst case" that never actually stresses cash, a margin that quietly improves every year for no modelled reason. This skill builds one sheet: a combined Summary at the top giving the whole-sheet picture at a glance, then two clearly separated sections below it — one that tests the **inputs** (does each driver sit in a sensible range), and one that tests the **outputs** (does what those drivers produce actually behave sensibly). Each section has its own header, table, items-to-verify, and key findings so it reads as a distinct check — only the top-level Summary counts are shared. The goal is a review deliverable a reviewer can trust and re-run, not a static opinion.

**Type:** Encoded Preference · Investigation / Triage

**Before outputting:** Re-check this sheet against skill-2 and skill-1.

## 1. When to Use

- Sense-checking a model's assumptions (wild growth rates, off percentages, missing tax) and/or its outputs (implausible trends, cash that never stresses, margins that don't hold up, scenarios that don't order correctly)
- Producing an auditable flag sheet before board, investor, or lender review
- Reviewing a model built by someone else, or re-reviewing one after changes
- Do NOT use to catalogue every input cell (that is skill 9), to find formula errors or balance sheet imbalance (that is skill 10), or to write narrative commentary explaining why numbers moved (that is skill 6)

If the user only asks about assumptions, build only the assumption section. If they only ask about outputs, build only the output section. If they ask generally ("reasonableness check", "sanity-check the model"), build both in the one sheet with a combined Summary.

## 2. Find the Source Sheets First — Do Not Assume Names

Sheet names, numbering prefixes, and how a model splits its calc sheets are **not fixed** — they vary between workbooks even within the EXL Cloud template family. One workbook has a single `4. Rev & COGS` sheet; another splits it into `4.a. Rev_Calc_TC` / `4.b. Rev_Calc` / `4.c. COGS_Calc_TC` / `4.d. COGS_Calc`; another calls them `Calc Revenue` and `Calc COGS`. Section numbering shifts too, and everything downstream of a split section shifts with it.

List every sheet tab and identify each sheet **by what is on it**, not by matching a literal name. The names below are examples, never lookup keys.

**Input/driver sheets** (feed the assumption section):
- Scenario Manager — scenario switches, Base/Best/Worst columns, live-case formula. Growth %, working capital days, valuation multiples, discount rate
- Revenue & COGS drivers — job targets, growth p.a., average values, teams, discount, CPI. May be one sheet or several, split apart or duplicated per tracking category
- Roles / Salary setup — salaries, rate increase %, PAYG %, FTE factor
- Tax — GST rate and the separate corporate income tax rate (often much further down the sheet than GST)
- Working Capital, Assets, Capital — days, depreciation life, debt facilities

**Output/summary sheets** (feed the output section):
- Consolidated output — full monthly Income Statement, Balance Sheet, Cash Flow, usually with a Forecast Flag or ACT/FCST row per period
- Annual / focused rollups — sheets rolling monthly output up to annual or a shorter focus window
- Executive / scenario dashboards — headline KPIs by scenario
- Export/reporting views, if present

Not every workbook has all of these as separate tabs. Match by content and header labels, and do not assume fixed row or column positions — follow the model's own pattern of referencing source by row label rather than hardcoding positions.

## 3. Build Steps

1. **Harvest inputs**: key value-drivers only (revenue/cost/opex growth, job and project drivers, CPI, margin, WC days, salaries and rate increase, GST, corporate tax, depreciation life, discount rate and multiples). Skip placeholders and structural constants
2. **Harvest outputs**: headline metrics across the model horizon, per scenario where available — revenue, gross margin %, EBITDA and EBITDA margin %, net cash position (minimum and closing). Prefer pulling from wherever the model already consolidates these
3. **Create one sheet**: `Assumption & Output Reasonableness Check`, positioned near the front in the workbook's existing numbering convention, tab colour `#2D6A4F`
4. **Write a sheet-level header block**: model name via `=pModelName`, title, entity link, live-scenario formula, generated-date note
5. **Build the assumption section**: section header banner, auto-check table (§5), flag logic (§6), items-to-verify (§7), key findings (§8)
6. **Insert a clear section break**: 2-3 blank rows plus a full-width divider row carrying the next section's title
7. **Build the output section** below the break, mirroring the same structure (§9-§12)
8. **Write the combined Summary block** (§4) directly under the sheet-level header, referencing the actual Status ranges of both sections
9. Apply conditional formatting, number formats, borders, and wrap to both sections. Re-read to confirm rendering. Log to Claude Log

**Before step 1, check whether the sheet already exists from a previous run.** If it does, this is a **refresh, not a rebuild**:

- Carry existing Reasonable Low/High bounds forward unchanged — only set a new bound for a genuinely new row
- Refresh Live Value formulas, Status flags, Summary counts, and Notes — but not the bounds themselves
- Only remove a row if its source no longer exists in the model
- A rerun should produce a new flag only when a Live Value moved outside an unchanged bound, never because the bound moved

## 4. Combined Summary Block

One Summary, directly under the sheet-level header and above both sections — not one per section.

| Label | Formula (adjust ranges to actual table sizes) |
|---|---|
| Total checks tested | `=COUNTA(assumption_status_range)+COUNTA(output_status_range)` |
| Passed | `=COUNTIF(assumption_status_range,"OK*")+COUNTIF(output_status_range,"OK*")` |
| Flagged for review | `=COUNTIF(assumption_status_range,"FLAG*")+COUNTIF(output_status_range,"FLAG*")` |
| Judgemental items to verify | `=COUNTA(assumption_items_range)+COUNTA(output_items_range)` |

Status text uses a tick or warning symbol in the sheet itself — match the COUNTIF wildcards to whatever symbol you actually write. Use one consistent label for "did this pass" across both sections. Write this block last, once the real row ranges are known. If the user only asked for one section, this block reports that section's counts only.

## 5. Assumption Section — Check Table

| # | Category | Assumption | Source Cell | Live Value | Reas. Low | Reas. High | Status | Notes / Rationale |
|---|----------|-----------|-------------|-----------|-----------|-----------|--------|-------------------|
| 1 | Revenue | Revenue growth p.a. — Base | `1.b. Scen_Mngr!E44` | `='1.b. Scen_Mngr'!E44` | 0.05 | 0.15 | Above range | Uniform 30% across every stream — no differentiation |
| 2 | Tax | Corporate income tax rate | `9. Tax!C265` | `='9. Tax'!C265` | 0.25 | 0.30 | Below range | 0% — no company tax modelled |

(Source cells above are illustrative — use whatever sheet names the workbook actually has.)

- Live Value is always a formula to source, never a pasted number
- Reasonable Low/High are **blue `#0000FF` inputs** so the reviewer can tune the bar
- Per-row number formats: percentage format for rates, integer for days and years, multiple format for multiples

### Setting Reasonable Low/High — History First

1. **If the workbook has historical ACT data for this metric**, derive the bound from that history — for example Low/High as the historical average minus/plus half the historical average, or the observed min/max widened by a stated tolerance. State the method in Notes so a refresh can reproduce it
2. **If there is no usable ACT history**, fall back to the fixed defaults below. Do not re-derive them each run
3. Once set, a bound stays fixed until the reviewer edits it or the source data structure changes

**Fixed defaults — statutory and market-set assumptions** (adjust jurisdiction-specific rows if the entity is not Australian):

| Assumption | Default Low | Default High | Why fixed |
|---|---|---|---|
| GST rate | 10% | 10% | AU statutory flat rate — anything else is a hard anomaly |
| Corporate income tax rate | 25% | 30% | AU base-rate vs full company tax rate |
| Payroll tax rate (where applicable) | 4.0% | 6.5% | Typical AU state payroll tax range |
| Discount rate / WACC | 8% | 20% | Typical SME valuation range |
| EV/Revenue multiple | 0.5x | 4.0x | Typical SME/mid-market range |
| EV/EBITDA multiple | 4.0x | 12.0x | Typical SME/mid-market range |
| Depreciation life (years) | 3 | 15 | Equipment through fit-out |

**Fixed fallback defaults — business performance assumptions**, used only where there is no ACT history:

| Assumption | Default Low | Default High |
|---|---|---|
| Revenue growth p.a. — Base | 3% | 20% |
| Revenue growth p.a. — Best | 10% | 35% |
| Revenue growth p.a. — Worst | -10% | 5% |
| COGS/opex growth p.a. | 0% | 15% |
| Salary rate increase p.a. | 2% | 6% |
| Working capital days (receivable/payable/inventory) | 15 | 90 |

## 6. Assumption Flag Logic

Status formula — put the direction in the flag so both tails are caught:

`=IF(F15="","",IF(F15<G15,"Below range",IF(F15>H15,"Above range","Within range")))`

Conditional formatting on the Status column: any out-of-range flag red (`#F8CBAD` fill, `#C00000` text); within range green (`#C6EFCE` fill, `#2D6A4F` text). This Status range feeds the combined Summary — do not build a separate summary block here.

## 7. Judgemental Items to Verify (Assumptions)

A range check cannot score everything. List, with source cell and observation, the things a human must eyeball: placeholder salary step-ups, all-zero FTE factors, blank payroll tax rate, inactive debt facilities, oddly low unit values, valuation method divergence.

The observation text goes in a cell **merged across the same width as the Notes column above it**, so a sentence or two reads as 1-2 lines. If it wraps past 3-4 lines, tighten the text rather than letting the row grow tall.

## 8. Key Findings Block (Assumptions)

3-7 plain-English bullets, highest impact first, each naming the fix (for example "Corporate tax 0% — set to 25%/30%"). Colour the top offender red and the "no action / reasonable" line green.

Each bullet is one row, and its text goes in a cell **merged across the full table width** with wrap text on — never a single narrow column. A finding written into a narrow cell wraps into 8-10 short lines and buries the point a reviewer is meant to read first. Keep each bullet to 1-2 sentences.

## 9. Output Section — Check Table

Outputs are not a single static number to bound like an assumption — a trend is tested as a shape, cash as behaviour over time, scenarios as a relationship between three values. Adapt the row layout to what is being tested, but keep the discipline: every Live Value is a formula to source, every bound is an editable blue input, every row gets an auto Status.

| # | Check type | Metric | Source Cell(s) | Live Value | Test | Status | Notes |
|---|-----------|--------|-----------------|-----------|------|--------|-------|
| 1 | Trend | Revenue YoY growth, FY26 | `='1.d. Fin_Annual'!I19/'1.d. Fin_Annual'!H19-1` | 0.42 | Low -0.2 / High 0.5 | Within range | |
| 2 | Margin | Gross margin %, FY27 | `=('1.d. Fin_Annual'!J19-COGS)/'1.d. Fin_Annual'!J19` | 0.61 | Low 0.35 / High 0.55 | Above range | Check COGS driver did not drop |
| 3 | Cash | Minimum cash, Worst scenario | `=MIN('13. Output'!H80:AZ80)` | -250,000 | Flag if never below 0 across horizon | No stress observed | Worst case never stresses cash |
| 4 | Scenario ordering | EBITDA FY28, Worst/Base/Best | three cells | see formula | Worst <= Base <= Best | Ordered | |

Group rows by check type (Trend / Cash & Liquidity / Margin and Ratio / Scenario Ordering) so a reviewer can scan one category at a time.

## 10. Output Flag Logic

- **Trend/trajectory**: compute year-on-year growth for headline metrics and flag any single-period change outside an editable band. Only test columns that are **purely forecast** — check the Forecast Flag on every underlying month feeding that column, not just the column header
- **Partial periods**: if the first or last year is not a full 12 months, its YoY growth is not comparable. Either set Status to "Partial period, not comparable" with no band test, or move the row to items-to-verify. Do not flag it with the same band — a 6-month year showing -47% is a period-length artefact, not a real decline
- **Margin/ratio bands**: same Status formula as §6, applied to a computed ratio (gross margin, EBITDA margin, leverage, coverage) instead of a raw input
- **Cash & liquidity**: test minimum cash under the Worst scenario across the horizon. Flag both directions — a minimum that goes materially negative (real liquidity risk) and a Worst case whose minimum never drops meaningfully below Base (no genuine stress case, which is itself a modelling gap)
- **Scenario ordering**: `=IF(AND(Worst<=Base,Base<=Best),"Ordered",IF(Worst=Base,"Converged","Ordering issue"))` at each check point (FY-end EBITDA, closing cash). An inversion or convergence usually means a scenario driver is not flowing through
- Conditional formatting: same red/green convention as §6. This Status range also feeds the combined Summary

**Output bounds follow the same history-first rule as §5.** Derive YoY growth bands, margin bands, and cash thresholds from the business's own ACT history where it exists. Where there is no historical counterpart, fall back to fixed defaults in the same spirit — revenue and EBITDA YoY growth -20% to 50%, a gross margin band derived from the assumption section's own COGS growth bound (so the two sections do not quietly disagree), EBITDA margin 5%-35% unless historicals say otherwise.

## 11. Judgemental Items to Verify (Outputs)

Things a formula can pass as "in range" that still deserve a human look: a margin that is suspiciously flat year on year (may be hardcoded downstream rather than driven), a large step at the ACT to FCST boundary, an annual figure built from a partial first or last year, or a Worst scenario identical to Base rather than genuinely different.

Same layout rule as §7 — merged, full-width, reading as 1-2 lines.

## 12. Key Findings Block (Outputs)

3-7 plain-English bullets, highest impact first, in the same style as §8 — for example "Worst-case cash never goes negative, the scenario is not stressing the model" or "EBITDA margin improves every year with no offsetting driver, check for a hardcoded downstream override."

Same layout rule as §8 — merged, full-width rows.

## 13. Gotchas

- **Not every Scenario Manager cell is an input.** Some rows are output links back to a calc sheet. Reading a 0 there and flagging "0% wage growth" is a false positive — trace the formula to the real input sheet before flagging
- **The generic Scenario Manager growth % is often not the binding driver.** Revenue is frequently built from the Revenue and COGS driver sheets directly — that is where extreme growth rates hide, not in the tidy scenario summary block
- **When Revenue & COGS is split or duplicated per tracking category**, check every one of those sheets — a driver hiding on a second or third sheet is an easy miss
- **Growth rates on driver sheets are annual (p.a.)**, stepped once per year by the row logic — do not misread them as monthly
- **Corporate tax is not GST.** GST sits high on the Tax sheet; the company income tax rate is a separate input much further down. A 0% company rate is a real and easily missed anomaly
- **FTE factor 0 is not automatically wrong** — headcount may be driven from a separate calc sheet. Put it in items-to-verify, do not hard-flag
- **Outputs mix ACT and FCST periods.** Trend checks should run on forecast periods only. An ACT to FCST transition is expected to look different, though a large jump right at that boundary is worth a note
- **An aggregated column can itself be a blend of ACT and FCST months** — the current year is the classic case, 6 months of actuals plus 6 months of forecast rolled into one column. Testing it against the same band measures the transition, not a forecasting problem. Check the Forecast Flag on every underlying month; if mixed, exclude it from the auto band test or mark its Status distinctly
- **Annual figures are often built via SUM/OFFSET matched to source by row label.** If the first or last year has fewer than 12 months, its total is not comparable to a full year — check the period count before flagging it as unusually low or high
- **`worksheet.position` throws `InactiveWorkbook`** when another workbook has focus. Cell writes still succeed — wrap the reposition in try/catch and retry, do not abandon the build
- **Setting `numberFormat` on a full column (`"B:B"`) can throw `InvalidOperation`.** Use bounded ranges (`B2:B200`)
- **A worst case that still grows, or never stresses cash, is not a genuine downside.** Flag it in both sections where it is visible
- **Do not let the section break drift.** Write the output section's start position relative to where the assumption section actually ends, never hardcoded. The same applies to the combined Summary's ranges
- **Uniform growth across all streams**, or a margin that never moves, is itself a flag (lack of differentiation or a possible hardcode) even when the number looks plausible in isolation
- **Key Findings and items-to-verify text must be merged full-width**, never left in a narrow column. This is the most common formatting defect in this skill's output
- **Only one Summary block on the whole sheet.** Both sections feed the same combined Summary — do not let a per-section summary sneak back in
- **A rerun that finds new problems every time usually means the bounds are not stable**, not that the model keeps breaking. Preserve existing bounds on refresh so a new flag always means something in the model actually changed

## 14. Constraints

- This skill ADDS one sheet — it does not modify existing cells, references, or named ranges
- Every derived cell is a formula (Live Value links to source, Status and Summary are formulas). No pasted numbers
- Benchmark bounds are editable inputs, not hardcoded into the Status formula
- Benchmark bounds, once set, are the standard — a rerun refreshes Live Values and Status against the existing bounds, it does not redraw the bounds
- Verify by re-reading rendered values and text before reporting done, for the combined Summary and both sections
- Do NOT freeze panes, hide gridlines, or modify sheet view settings
