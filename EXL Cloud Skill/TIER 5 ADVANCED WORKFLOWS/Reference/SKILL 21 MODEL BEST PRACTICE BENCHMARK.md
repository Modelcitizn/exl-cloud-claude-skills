---
name: skill-21-exl-cloud-model-best-practice-benchmark
description: "Use when the user says 'run the benchmark', 'model health check', 'benchmark this model', 'how well is this model built', 'build the evidence map' or invokes /model-best-practice-benchmark. Automatic in-model health check of any Excel model when the Benchmark workbook isn't connected: auto-detects model type and company; builds a direct-scan BM Direct Evidence Map (14 formula filters), scores 40-60 criteria, adds BM Control, Assessment, Findings Log, Remediation Plan (hours only, no pricing), Data Plumbing and Validation Checks, then checks every BM tab against EXL Cloud standards. Do NOT use when the Benchmark workbook is connected (run the benchmark from that workbook instead), to fix models, for version diffs or for single-file audits."
---

# Model Best Practice Benchmark

The benchmark (BM) is a **health check** of how well a model is built against best practice. It does not price the work. It returns:
- a score
- a list of findings, each with a location and a fix
- a phased fix plan with **estimated hours** (effort only, no rate and no fee)

**Type:** Encoded Preference · Investigation / triage · Workflow automation

**Before outputting:** Re-check every BM tab against skill-2 and skill-1 (§2.1).

**Dependencies:**
- **Requires:** `exl-cloud-tier-1` (skill 1 and skill 2 Reference files) for formatting and the final standards check. Fallback: `skill-2-exl-cloud-best-practice-modelling` (§2.1).
- **Hands off to:** the full Benchmark workbook run (not part of this skills library) when the benchmark workbook is the active file or a connected peer.
- **After:** the user deletes the seven BM tabs and the `BM_` names before the file returns to the client. Fixing the findings is a separate job, not covered by this skill.

## 1. When to Use

- The active file is the model under review and no benchmark workbook is open or connected.
- The user wants a benchmark score / health check, remediation plan, data plumbing inventory or direct evidence map in this workbook.
- Any Excel model: G2/G3 reporting packs, EXL Cloud / Model Citizn 3-way models, or generic models (criteria that don't fit are marked not applicable).
- Do NOT use to fix the model, for version diffs, for single-file audits or for official 175-criterion runs (those are run from the Benchmark workbook).

**Rule:** never assume sheet names, names, addresses or row positions from a previous workbook. Discover everything from the open file every run. Compute rows from a row index in code, and link between tabs through `BM_` names. Positions carried over from another model are the most common cause of broken links.

## 2. Standing Rules

### 2.1 EXL Cloud standards gate (mandatory)

Every BM tab this skill creates is an EXL Cloud sheet, so the EXL Cloud standards apply in full.

1. **Load before formatting** (before step 3.3).
   - Call `read_skill` on `exl-cloud-tier-1` and open its Reference files for skill 1 (Foundations) and skill 2 (Best Practice Modelling).
   - If `exl-cloud-tier-1` isn't available, call `read_skill` on `skill-2-exl-cloud-best-practice-modelling` instead, and say so in the summary.
   - If neither loads, still build the tabs, but report "NOT loaded" and don't claim compliance.
   - Take every value from the loaded file: colour legend, fills, fonts, number formats, sheet view defaults, header bands. This skill deliberately lists none of them, so there is nothing to format from memory.
2. **Check before reporting done** (step 3.11). Check all seven BM tabs against two rule sets. Take each from whichever loaded file contains it, and note the file and section:
   | Check | Covers | tier-1 source | Fallback source (skill 2) |
   |---|---|---|---|
   | Skill 1 | hardcodes, formula discipline, structure: typed cells only from the §6 allowed list; derived values are formulas; no magic numbers inside formulas; `BM_` names for links between tabs | Skill 1 Reference file (if it has no hardcode rules, use the loaded file that does) | §6 Workings and Calculation Rules, §7 Hardcoded Values |
   | Skill 2 | colour legend (inputs, formulas, links), fonts, number formats, header fills, sheet view, tab and layout conventions | Skill 2 Reference file | §1 Colour Coding, §2 Font, §3 Number Format, §5.5 Sheet View Defaults |
   - Read the format properties back; don't rely on the write calls.
   - Fix failures on the BM tabs only, re-read to confirm, and keep a list of what each check changed.
3. **Report it.** The chat summary carries the "EXL Cloud standards" line (§4): the file and sections loaded, and what each check changed (e.g. "Skill 1: converted 3 typed totals on BM Findings Log to formulas. Skill 2: set dash-for-zero on BM Remediation Plan hours; no other changes."). If nothing loaded or a check wasn't run, say so plainly.

Done when: a standards file has been read this run, all seven BM tabs pass both checks on a re-read, and the summary carries the "EXL Cloud standards" line naming its source.

### 2.2 Other standing rules

- **No pricing:** no rate, `BM_Rate`, fee or $. This is a health check, not a quote.
- **Auto-detect** model type and company; no questions by default (§3.0).
- **One evidence source:** every BM tab links back to the read-only direct-scan evidence map.
- **Score on content, never existence:** a documentation or navigation criterion passes on what the sheet says, not on the sheet being there (§5).

## 3. Method

### 3.0 Detect and confirm (no questions by default)

1. Call `get_connected_agents`. If a benchmark workbook (Master Control / 3. Assessment / 7. Run Register) is present, stop and tell the user to run the benchmark from that workbook instead.
2. **Model type:**
   - **3-way model:** a calculated P&L, BS and CF, e.g. "NPAT", "Total Assets", "Retained Earnings", "Balance Check", "Closing Cash" on Core sheets.
   - **Reporting pack:** TB / Xero / Performance Reports / PQ feeds / control-period pivots, with no BS/CF engine.
   - **Generic:** neither pattern is met.
   - Record each signal as Sheet!Cell.
3. **Company**, in this priority order:
   1. names such as `Company` / `Entity` / `Client` / `Org*`
   2. label cells and the value next to them
   3. banners or legal-entity text in sheet names or cells
   4. the file-name stem
   Record the source.
4. Ask (one `ask_user_question`) only if the type or company is unclear or conflicting. If earlier BM tabs exist, ask: new run or recalibrate. Never delete them.
5. Present the plan in chat with the detected type, company and sources, and a note that the BM tabs will be formatted and checked under §2.1. Wait for approval.

Done when: detection is recorded and the plan is approved.

### 3.1 Discover the model (read-only direct scan)

Use `execute_office_js` with small returns, and store the working data in `blobs` (`bm_scan`, `bm_meta`). The sandbox has no `eval`, so define helpers inline in each call.
- **Sheets:** name, position, visibility, used range, protection, hidden rows/cols, charts, pivots and shapes.
- **Classify each sheet:**
  - **Core**
  - **Section divider:** `>>>` / `<<<X>>>` names, or no used range
  - **Duplicate copy**
  - **Add-in report:** dot-prefixed names
  - **Documentation:** Instructions, Overview, Mechanic Explainer, Formula Map, Change Log, plan / snapshot / Q&A sheets
  - **BM tab**
- **Core sheets:** read `formulasR1C1` / `formulas` / `valueTypes`, in chunks of 1,000 rows or fewer. Count:
  - formula, constant and error cells
  - distinct R1C1 patterns (first cell, formula and count)
  - patterns used once
- **Excluded sheets:** count formula cells with `getSpecialCellsOrNullObject(formulas)`.
- **Names:** total, hidden, `#REF!`, external, text-valued, and the `_xlfn` / `_xlpm` / add-in stubs. Count them before the build.
- Tables, pivots, queries, connections and external refs. Record API gaps as "Not exposed".
- Calc mode, iterative calc, `.xlsm` / VBA.
- **The model's own checks:** test whether each can actually fail. Look for:
  - constant expected values
  - checks skipped when an input is blank
  - partial coverage
  - text-valued aggregate names
  - a row compared with a row that is the same SUM
- **3-way models:** BS balances every period (and isn't a plug), cash ties, NPAT → RE roll-forward including period-1 opening RE, corkscrews, annual vs monthly.
- **Documentation sheets:** read their content (headings, steps, last-updated text), not just their names, so documentation criteria can be scored on content (§2.2).
- House style.

Record the evidence timestamp `dd mmm yyyy hh:mm:ss TZ`.

Done when: the per-sheet counts sum consistently and a chat summary gives counts with denominators.

### 3.2 Formula filters (unique R1C1 patterns, Core sheets only)

Run every filter on the pattern's R1C1 string. In R1C1, cell refs look like `RC`, `R[-1]C[2]`, `R5C3` and `R1:R5`: brackets straight after R or C are offsets, not external books, and there is no `$`. Strip string literals first (except for the Text filter).

| Filter | Detection rule | Confidence | Used for |
|---|---|---|---|
| Hardcoded | a digit remains after removing refs, sheet names, defined names, function names (e.g. LOG10, DAYS360) and quoted text | Medium | INP-04 |
| Externals | `[n]` or `[Book.xlsx]` directly before a sheet name and `!` (e.g. `[1]Sheet1!`, `'[1]My Sheet'!`); never an R1C1 offset such as `R[1]C` | High | FRM-03 |
| Functions | a `NAME(` call | Medium | FRM-06 |
| "Text" | a non-empty string literal | High | Remediation Plan only |
| Names | a token matches a defined name | Medium | FRM-08 |
| Intersheets | `!` outside quotes, ignoring error literals (`#REF!`, `#DIV/0!`, `#NUM!`, `#NULL!`) | High | Remediation Plan only |
| #REF! | contains `#REF!` | High | FRM-01 |
| Absolute | an absolute R1C1 part: `R5`, `C3` or `R5C3` with no brackets | High | Remediation Plan only |
| Array | `{…}` or SUMPRODUCT / MMULT / TRANSPOSE / FREQUENCY / LINEST | Low | Remediation Plan only |
| Table | structured reference | High | FRM-08 |
| User defined | not a built-in function (exclude LET/LAMBDA parameter names) | Medium | Remediation Plan only |
| Dynamic array | FILTER / SORT / UNIQUE / SEQUENCE / BYROW / BYCOL / LAMBDA / LET, or a spill ref (`R5C3#`) | Medium | Remediation Plan only |
| Volatile | OFFSET / INDIRECT / TODAY / NOW / RAND / RANDBETWEEN / CELL / INFO | High | FRM-07 |
| Error handling | IFERROR / IFNA / ISERROR / ISERR | Medium | OUT-05 |

- **Example cell:** the shortest pattern that plainly shows the trait (for Hardcoded, a business literal). Never reuse one generic cell. Write "None found" in grey when the count is 0.
- Also compute function combinations, long-formula counts and business-literal patterns.

Done when: every count is ≤ the unique patterns and each example has been checked by eye.

### 3.3 Create the BM tabs

Seven tabs at the end, in order: BM Control, BM Direct Evidence Map, BM Assessment, BM Findings Log, BM Remediation Plan, BM Data Plumbing, BM Validation Checks.
- **House style:** apply §2.1 step 1 before writing any formatting. Decide the spec once from the loaded file and apply it identically to all seven tabs.
- **Banner:**
  - B2 model name
  - B3 title (Lite)
  - B4 `="Evidence timestamp: "&BM_TS`
  - B5 company
  - B6 Run ID
  - B7 evidence basis line
  - F6 the BM error message linked to `BM_Err`
- **Names:** `BM_RunID`, `BM_Company`, `BM_ModelType`, `BM_TS`, `BM_W_Sev`, `BM_W_Val`, `BM_Thresh`, `BM_A_*`, `BM_FL_IDs`, `BM_FL_Hrs`, `BM_Err`, `BM_EM_Unique`, `BM_EM_FormulaCells`, `BM_EM_Filter`, `BM_EM_Count`, `BM_EM_IDs`, `BM_EM_Status`, `BM_EM_Names`, `BM_EM_Tables`, `BM_EM_Pivots`, `BM_EM_Queries`, `BM_EM_RefNames`, `BM_EM_TextNames`, `BM_RP_Filters`. Never `BM_Rate`.

Done when: the seven tabs sit last in order, every banner cell and `BM_` name resolves (no `#NAME?` or `#REF!`), and the formatting spec came from the file loaded under §2.1.

### 3.4 BM Direct Evidence Map

Typed scan values, formulas and cross-sheet links use the loaded file's colour legend; sheet names are hyperlinked.
- **A. Sheet scan (Core):** Sheet | Class | Formula | Constant | Unique patterns | Errors | Protected | Hidden rows/cols.
  - Total row (BYCOL) naming `BM_EM_FormulaCells` / `BM_EM_Unique`.
  - Extra rows: unique ÷ formula cells, unprotected share, used once, whole-workbook formula cells, Formula Map cells, function combinations, long-formula counts, business literals.
  - **Excluded sheets** table with a Total row.
- **B. Formula filters (14):** Filter | Rule | Count | % of unique | Indicates | Confidence | Example | Used for | Also linked in | How used.
  - Also linked in: `=IFERROR("BM Remediation Plan row "&INDEX(ROW(BM_RP_Filters),XMATCH([filters],BM_RP_Filters)),"Not in plan")`, where `[filters]` is the filter-name column.
- **C. Workbook checks:** names (total, hidden, #REF!, external, text, stubs), tables, pivots, queries, external links / refs, calc, iterative, VBA, CSE, connections, charts.
- **D. Criteria using this evidence:** ID | Criterion | Score | Basis | Direct source | Data used.
  - IDs: STR-01, STR-04, STR-06, INP-04, INP-05, FRM-01, FRM-03, FRM-05, FRM-06, FRM-07, FRM-08, OUT-01, OUT-04, OUT-05, AI-01 (`BM_EM_IDs`).
  - Criterion / score / basis are spill XLOOKUPs into BM Assessment.
- **E. Tie-out (9 rows, spilling PASS/FAIL):**
  1. max of the 12 original filter counts ≤ unique patterns
  2. unique patterns ≤ formula cells
  3. external refs ≤ unique patterns
  4. largest name subset ≤ total names
  5. error cells ≤ formula + constant cells
  6. sheets scanned + excluded = `SHEETS()`
  7. Section D criteria found in BM Assessment = `ROWS(BM_EM_IDs)`
  8. max(Volatile, Error handling) ≤ unique patterns
  9. Section D criteria with basis "Limited" = 0
  - Status `BM_EM_Status`: "OK – all n tie-outs pass" / "CHECK – n failing".

Done when: tie-outs 1–6 and 8 pass on a re-read. Tie-outs 7 and 9 are confirmed in step 3.5, once BM Assessment exists.

### 3.5 BM Assessment

- Choose 40–60 criteria for the detected model type, covering:
  - Governance & Documentation
  - Structure & Flow
  - Inputs
  - Formula Integrity
  - Consistency
  - Checks
  - Data & Feeds
  - Outputs & Dashboards
  - Formatting
  - AI Governance
  - 3-Way Integrity (Applicable 0 if not a 3-way model)
  Always include the 15 map criteria.
- **Typed columns:** ID, Category, Criterion, Severity, Applicable, Score (0–4), Basis (Direct scan / Direct inspection / Limited), Location, Finding, Fix, Status, Fix hrs.
- **Evidence column:** formulas over `BM_EM_*` for the map criteria; typed with a denominator for the rest.
- **Formula columns:** Weight, Weighted pts, Max pts, Flag.
- **Scoring:**
  - ≤5% exceptions = 3; systematic = 1 or 0; 4 only with zero known exceptions.
  - Sample the Medium-confidence filters before scoring.
  - Score documentation and navigation criteria on what the sheets say (from §3.1), never on the sheet list alone.
  - One issue per criterion; labelled input constants are not a fail.
  - Scores are always typed, never formulas (formulas here would create circular references through the map).
  - No placeholders. Finish with a calibration pass.

Done when: every applicable criterion has a typed score of 0–4, a basis and evidence with a denominator; the calibration pass is done; and map tie-outs 7 and 9 pass.

### 3.6 BM Control

- **Engagement block:**
  - model name, file name
  - company and model type, each with its source
  - Run ID (`R[nn]-Lite`)
  - review depth, tabs at scan (plus the live `SHEETS()` count), evidence timestamp
  - reviewer (input cell, left for the user to confirm), names at scan
  - no rate
- **Weights:** Critical 4 / High 3 / Medium 2 / Low 1. Threshold ≤ 2.
- **Headline:** criteria, applicable, scored, unscored, weighted %, Critical %, findings, priority findings, estimated fix hours, Limited, In review, map status, Direct scan count, `BM_Err`.
- Category table with a Total row; run register (values, Closed); run log.

Done when: the headline and category Total agree with BM Assessment (same criteria, applicable and scored counts), and `BM_Err` shows on the headline.

### 3.7 BM Findings Log

- One row per applicable criterion scored ≤ threshold.
- ID typed; everything else looked up, including hours. No fee.
- Total row. Names `BM_FL_IDs` / `BM_FL_Hrs`.

Done when: the number of Findings rows equals the criteria flagged in BM Assessment, the IDs are unique, and the hours Total is a formula.

### 3.8 BM Remediation Plan

- **A. Phases P0–P6:**
  - P0 Baseline & governance
  - P1 Integrity
  - P2 Make the checks bite
  - P3 Strip business hardcodes
  - P4 Separate inputs & restyle
  - P5 Formula consistency & structure
  - P6 Scenarios & outputs
  Show tasks, Critical/High and hours per phase, with a Total row, the plan − Findings hours tie (= 0) and "N of N findings planned".
- **B. Task list:** typed planning fields; Category, Severity, Score, Gap pts, Location, Action and Hrs looked up. Never type hours.
- **C. "Formula filter summary (direct scan)":** 14 rows (`BM_RP_Filters`).
  - Columns: Filter | Direct count | % of unique | Target after remediation | Criteria driven | Phase.

Done when: plan − Findings hours = 0, "N of N findings planned" shows the same N twice, and section C has 14 rows.

### 3.9 BM Data Plumbing

- **Thresholds (input cells):**
  - as-at date (typed)
  - stale days 90
  - Large 10,000 / Medium 1,000 cells
  - calc cols for High 3
  - constant share 80% / 30%
  - query counts 30 / 10
  - M length 3,000 / 800
- Control totals: links to map Section C.
- **A. Summary:** formulas over the registers, with the overall and PQ complexity bands.
- **Registers:** B Feed & input sheets, C Tables, D Power Query ("No queries" if none), E Pivots, F External links, G Defined names (stubs labelled "Excel / add-in stub"), H Export limitations.
- Conditional formats red / amber.
- One source note on each register's top-left header cell: "Source: direct Office.js scan of [file], [N] rows, run [date time TZ]. Run by Claude via connector. Full scan details on BM Control run log."

Done when: every register count equals its control total and each register carries the source note.

### 3.10 BM Validation Checks

- **Columns:** # | Check | Value | Expected | Enabled | Error `=IF(Enabled=1,IF(Value<>Expected,1,0),0)` | Note.
- C10 `BM_Err`; C11 the failing check numbers.
- **Hard checks:**
  - unscored = 0
  - missing basis or evidence = 0
  - scores in 0–4
  - Findings rows = flagged
  - Findings IDs in library; no duplicates; IDs still flagged
  - plan − Findings hours = 0; every finding in the task list; phase = task hours
  - Run ID populated
  - model type valid; company populated
  - category total = library total
  - Data Plumbing counts = control totals; no errors on Data Plumbing, Assessment, Findings or Plan
  - map status OK; no errors on the map
  - map criteria all found and none Limited
  - section C filters = 14
- **WARN rows (Enabled 0):** text names, deliberate `#N/A` cells, the model's own error count, external links, duplicate tabs.
- Error scans start at row 10 or below. Don't touch the model's own checks sheet.

Done when: every hard check is Enabled and `BM_Err` = 0, or each failing check is named and explained in chat.

### 3.11 Verify

- Re-read the Control headline, the map tie-outs and status, the Findings total, the plan tie and section C, the Data Plumbing summary and Validation.
- `getImage` BM Control and the map in blocks of 50 rows or fewer.
- List the sheets to confirm the seven BM tabs are last and in order.
- **Search every BM tab:** no rate, fee or $.
- **EXL Cloud standards check:** run §2.1 step 2 on all seven BM tabs.

Done when: every check above passes on a re-read, and the skill 1 / skill 2 change lists are written down.

## 4. Chat summary template

```
Run R[nn]-Lite - [weighted]% weighted (Critical [x]%) | [n]/[n] applicable scored | [n] benchmark errors
Detected: [model type] (signals: [cells]) | Company: [name] (from [source])
Evidence: BM Direct Evidence Map - [unique] unique patterns / [formula] formula cells across [n] Core sheets | tie-out [OK/CHECK]
Top findings: [3-6 bullets, priority first, with Sheet!Cell]
Data Plumbing: [n] tables | [n] queries | [n] pivots | [n] external links → Overall [band]
Remediation: [n] findings, P0-P6, [hrs] estimated hours
EXL Cloud standards: [exl-cloud-tier-1 / skill-2 fallback] loaded ([sections]) | Skill 1 checked – [what changed, or "no changes"] | Skill 2 checked – [what changed, or "no changes"]   (or: NOT loaded/checked – [reason]; compliance not claimed)
To do: confirm reviewer; open questions
Before returning the file: delete the 7 BM tabs and BM_ names
Verified: [ranges re-read]
```

Write in en-AU unless the workbook uses another variety. Cite cells as Sheet!Cell. Each finding states the issue, the evidence with a denominator, and the fix.

## 5. Gotchas

- **Not the official benchmark.** Label everything "Lite"; scores aren't comparable with the official Benchmark workbook run or across models.
- **Health check, not a quote.** Never add a rate, fee or cost.
- **EXL Cloud formatting from memory is a fail.** Always load the standards file first (§2.1), even if the hex codes seem familiar. An earlier version broke this rule by listing the spec three times in the skill; this version lists none. Never report the run as done without the skill 1 / skill 2 line in the summary.
- **Don't invent a link colour.** An earlier version required green cross-sheet links, but skill 2 defines no green-link convention (black for formulas, red for external links). Use only what the loaded file defines.
- **The standards check applies to the BM tabs, not the model.** Don't restyle or fix the client's sheets; gaps in the model's own formatting become findings (Formatting category), not edits.
- **Existence isn't content.** In one official benchmark run, documentation criteria were scored from the sheet list ("Instructions exists") and the documentation category came out at 53% instead of 75%. Read documentation sheets before scoring them.
- **R1C1 has no `$` and uses brackets for offsets.** Filtering R1C1 strings for `$` returns 0 Absolute patterns, and treating every `[n]` as an external book counts `R[1]C` as an external ref. Tie-out 3 won't catch the second error, because the inflated count still sits under the unique total. Use the §3.2 rules.
- **The model's own tab-count check fails while the BM tabs exist** (Workbook Overview vs `SHEETS()`). Report it as a WARN and say so in chat.
- **Detection can mislead.** A "Balance Sheet" report tab isn't a 3-way model unless BS and CF are calculated.
- **Checks that can't fail**, e.g. a cash check where the compared row is itself `=SUM` of the same rows.
- **Text-valued aggregate names:** flag them under Names and Checks.
- **Labels starting with `#`, `=` or `.`:** write them as text (`@`). The "#REF!" filter label is text, not an error.
- **HYPERLINK may be blocked:** use `range.hyperlink`. There is no `eval`.
- **BYCOL / LAMBDA adds hidden names:** report the name count as at scan time.
- **Scores are typed;** only evidence text reads the map.
- **Deleting rows or columns on BM tabs must shift cells,** then re-check the spill anchors, `BM_` ranges and banner links.
- **Remediation hours come from BM Assessment by formula.** Never reuse a generic example cell.

## 6. Constraints

- Don't edit existing sheets, names, checks, macros or VBA. Additions are limited to the seven BM tabs, the `BM_` names and Excel's `_xl*` names. The file goes back to the client, so the review must leave no trace once the BM tabs are deleted.
- Typed cells are limited to: engagement fields, weights, threshold, raw scan values, Assessment typed columns, plan planning fields and filter targets, and Data Plumbing thresholds / register values.
- No rate, fee or currency.
- No cell notes on the map; the map is evidence, and notes hide it from the tie-outs and the search in §3.11. Use row grouping, never hidden rows, so nothing on a BM tab is invisible to the reviewer.
- The EXL Cloud standards gate (§2.1) is mandatory on every run.
- Always end with the reminder to delete the seven BM tabs and `BM_` names before the file goes back to the client.
