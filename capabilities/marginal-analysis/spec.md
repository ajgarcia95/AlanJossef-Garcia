---
type: spec
capability: marginal-analysis
engagement: perfect-competition
date: 2026-10-16
status: audited             # draft | built | audited
built_by: Claude (Claude Code, Opus 5.5), generated from this spec as committed
---

# Perfect Competition — model spec

## Purpose

The farm is deciding on what combination of beds will ultimately produce the best profit. This is determined through a combination of things like labor costs, and revenue from each style bed. The finished model should show us the best mix, overall profit/revenue with the use of properly executed man hours through our own labor as well as hiring temps at a set rate.

## Inputs

| Name | Value | Unit | Source (case scenario) |
|---|---|---|---|
| `TOM_PRICE` | 8,800 | $/bed | Case scenario, "The givens" crop table |
| `CAR_PRICE` | 2,094 | $/bed | Case scenario, "The givens" crop table |
| `MES_PRICE` | 2,700 | $/bed | Case scenario, "The givens" crop table |
| `TOM_HRS` | 2.5 | field hrs/week/bed | Case scenario, "The givens" crop table |
| `CAR_HRS` | 0.833 | field hrs/week/bed | Case scenario, "The givens" crop table |
| `MES_HRS` | 1.25 | field hrs/week/bed | Case scenario, "The givens" crop table |
| `TOM_FERT` | 880 | $/bed | Case scenario, "The givens" crop table |
| `CAR_FERT` | 440 | $/bed | Case scenario, "The givens" crop table |
| `MES_FERT` | 880 | $/bed | Case scenario, "The givens" crop table |
| `TOM_DIM` | 0.10 | diminishing-returns rate per bed (10%) | Case scenario, "The givens" crop table |
| `CAR_DIM` | 0.025 | diminishing-returns rate per bed (2.5%) | Case scenario, "The givens" crop table |
| `MES_DIM` | 0.0125 | diminishing-returns rate per bed (1.25%) | Case scenario, "The givens" crop table |
| `TOM_CAP` | 20 | beds (max) | Case scenario, "The givens" crop table |
| `CAR_CAP` | 20 | beds (max) | Case scenario, "The givens" crop table |
| `MES_CAP` | 30 | beds (max) | Case scenario, "The givens" crop table |
| `WEEKS` | 36 | weeks/season | Case scenario, farm parameters |
| `TOTAL_BEDS` | 64 | beds available | Case scenario, farm parameters |
| `FIXED_COST` | 20,000 | $/season | Case scenario, farm parameters |
| `OWN_HRS` | 720 | farmer field hrs/season | Case scenario, farm parameters |
| `OWN_RATE` | 34.72 | $/hr (implied) | Case scenario, farm parameters |
| `TEMP_RATE` | 17.36 | $/hr | Case scenario, farm parameters |
| `TEMP_HRS` | 1,440 | hrs per temp worker/season | Case scenario, farm parameters |
| `MAX_TEMPS` | 4 | temp workers (max) | Case scenario, farm parameters |
| `OTHER_VAR_COST` | 0 | $/season | None in the case scenario; placeholder for future variable costs |
| `CHECK_PROFIT` | 42,762 | $ | Course Stage 2 published check figure |
| `CHECK_TOL` | 25 | $ | My chosen tolerance for the profit check |

## Structure

The workbook has six parts, one sheet each:

1. **Inputs**: the 26 named case numbers from the table above, so nothing is typed into formulas.
2. **Cost structure**: labor hours and costs for the chosen mix.
3. **Marginal-cost schedules**: the cost of each extra bed, for each crop. One separate table per crop (tomatoes, carrots, mesclun). Each table has one row per bed number, q = 1 up to that crop's cap, and these columns: Bed (q), standalone labor hours (`SA_LABOR`), standalone cost (`SA_COST`), marginal cost (`MC`), price, and "Pays?" (yes if MC ≤ price).
4. **Optimization**: the three bed counts Solver changes (`TOM_BEDS`, `CAR_BEDS`, `MES_BEDS`), and the profit it maximizes. The file opens with the bed counts at 10 / 20 / 30.
5. **Checks**: one pass/fail cell for each of these seven checks:
   1. 0 ≤ `TOM_BEDS` ≤ `TOM_CAP`
   2. 0 ≤ `CAR_BEDS` ≤ `CAR_CAP`
   3. 0 ≤ `MES_BEDS` ≤ `MES_CAP`
   4. `TOM_BEDS` + `CAR_BEDS` + `MES_BEDS` ≤ `TOTAL_BEDS`
   5. `TEMPS_NEEDED` ≤ `MAX_TEMPS`
   6. each bed count is a whole number
   7. `PROFIT` within ±`CHECK_TOL` of `CHECK_PROFIT` (i.e. ±$25 of $42,762)
6. **Summary**: a one-page dashboard showing every output listed under Outputs.

Color coding:

- Inputs stay plain, with no color.
- Each crop has its own color on its column headers and labels only: tomatoes pink, carrots orange, mesclun teal.
- Every formula cell has a plain light yellow fill, meaning it should not be touched unless needed. Crop colors are never used as a formula cell's fill.
- On the Checks sheet, a passing check shows green and a failing check shows red. Green and red are used for nothing else.

## Calculation logic

Decision variables (the cells Solver changes): `TOM_BEDS`, `CAR_BEDS`, `MES_BEDS`.

**Labor hours per crop**

```
TOM_LABOR = TOM_BEDS × TOM_HRS × WEEKS × (1 + TOM_DIM) ^ TOM_BEDS
CAR_LABOR = CAR_BEDS × CAR_HRS × WEEKS × (1 + CAR_DIM) ^ CAR_BEDS
MES_LABOR = MES_BEDS × MES_HRS × WEEKS × (1 + MES_DIM) ^ MES_BEDS
```

**Splitting hours between me and temps**

```
TOTAL_LABOR  = TOM_LABOR + CAR_LABOR + MES_LABOR
OWN_USED     = MIN(TOTAL_LABOR, OWN_HRS)
TEMP_LABOR   = MAX(0, TOTAL_LABOR − OWN_HRS)
TEMPS_NEEDED = ROUNDUP(TEMP_LABOR / TEMP_HRS)
```

Add together the labor hours for tomatoes, carrots, and mesclun to get the total farm labor hours required. You work the actual total labor hours required by the farm up to a maximum cap of 720 hours. Temp hours equal total farm hours minus your 720 hours, but if total farm hours are 720 or fewer, temp hours are zero. Divide total temp hours by 1,440 hours per worker, and always round fractional results up to the next full worker.

**Costs and profit**

```
REVENUE    = TOM_BEDS × TOM_PRICE + CAR_BEDS × CAR_PRICE + MES_BEDS × MES_PRICE
FERT_COST  = TOM_BEDS × TOM_FERT  + CAR_BEDS × CAR_FERT  + MES_BEDS × MES_FERT
LABOR_COST = OWN_USED × OWN_RATE + TEMP_LABOR × TEMP_RATE
PROFIT     = REVENUE − LABOR_COST − FERT_COST − OTHER_VAR_COST − FIXED_COST
```

Multiply the number of beds planted for each crop by its revenue (or price) per bed, then add all three crop revenues together to get total season revenue. Multiply the fertilizer cost per bed for each crop by the number of beds planted of that crop, then sum those costs across all three crops. Subtract all operating expenses (total labor cost, total fertilizer cost, any other variable costs (kept at $0 as a placeholder, since the case has none), and the $20,000 in fixed costs) from total season revenue. Temps are paid only for the hours they actually work.

**Marginal cost per bed (standalone schedules)**

One schedule per crop, for q = 1 up to that crop's cap (`TOM_CAP`, `CAR_CAP`, `MES_CAP`). Shown for tomatoes; carrots and mesclun are identical with `CAR_` and `MES_`.

```
TOM_SA_LABOR(q) = q × TOM_HRS × WEEKS × (1 + TOM_DIM) ^ q
TOM_SA_COST(q)  = MIN(TOM_SA_LABOR(q), OWN_HRS) × OWN_RATE
                + MAX(0, TOM_SA_LABOR(q) − OWN_HRS) × TEMP_RATE
                + q × TOM_FERT
TOM_MC(q)       = TOM_SA_COST(q) − TOM_SA_COST(q − 1),   with TOM_SA_COST(0) = 0
TOM_CROSS       = the last q before TOM_MC(q) first exceeds TOM_PRICE
```

`TOM_CROSS` is 0 if even the 1st bed's MC exceeds `TOM_PRICE` (the crop never pays), and equals `TOM_CAP` if MC never exceeds `TOM_PRICE` up to the cap. The `(q)` formulas are built as columns of the per-crop tables described under Structure, not as Excel functions.

Marginal cost of the 5th tomato bed is the additional total cost the farm incurs by planting 5 beds of tomatoes instead of 4. Labor plus fertilizer go into it; fixed costs do not. Calculating a crop's schedule as a "standalone" curve means evaluating that crop in complete isolation (e.g., setting Carrot Beds = 0 and Mesclun Beds = 0), so my 720 hours go to that crop first.

## Conventions

- **Costing order:** my own 720 hours (`OWN_HRS`) are used before any temp hours.
- **Temp pay:** temps are paid only for the hours they actually work (`TEMP_LABOR × TEMP_RATE`), not a full `TEMP_HRS` each.
- **Per-crop P&L:** labor is charged to each crop at the blended rate, the same rate for every crop:
  ```
  BLENDED_RATE   = LABOR_COST / TOTAL_LABOR      (0 if TOTAL_LABOR = 0)
  TOM_LABOR_COST = TOM_LABOR × BLENDED_RATE      (same for CAR_, MES_)
  TOM_PROFIT     = TOM_BEDS × TOM_PRICE − TOM_BEDS × TOM_FERT − TOM_LABOR_COST
  ```
  The own/temp split is a farm-level fact, not per crop.
- **Marginal-cost schedules** are standalone: each crop is evaluated as if it were the only crop planted.
- **Whole beds:** `TOM_BEDS`, `CAR_BEDS`, `MES_BEDS` are whole numbers from 0 up to each crop's cap.
- **Rounding:** full precision in every calculation; round only what is displayed (dollars to whole numbers, hours to one decimal).
- **Zero beds:** 0 beds of a crop means 0 labor hours and 0 cost for that crop; no formula may return an error at 0.
- **Fixed costs are not allocated to crops.** The Summary shows `TOM_PROFIT`, `CAR_PROFIT` and `MES_PROFIT`, then a separate line subtracting `OTHER_VAR_COST` and `FIXED_COST`; together these add up to `PROFIT`.
- **Named ranges:** every input and every named result is a real Excel named range (Name Manager), and formulas use the names, e.g. `=TOM_BEDS*TOM_PRICE`, never cell addresses like `=B4*C7`.
- **Starting values:** the workbook opens with `TOM_BEDS` = 10, `CAR_BEDS` = 20, `MES_BEDS` = 30.

## Validation rules

Acceptance criteria (written before the build):

1. **Optimal mix:** `TOM_BEDS` = 10, `CAR_BEDS` = 20, `MES_BEDS` = 30 (60 beds).
2. **Season profit:** `PROFIT` = $42,762, within a tolerance of ±$25 (allows for rounding in `CAR_HRS` = 0.833). Displayed rounded to whole dollars.
3. **Standalone crossings:** `TOM_CROSS` ≈ 10, `CAR_CROSS` ≈ 10, `MES_CROSS` ≈ 6.
4. **Hand check at q = 1:** `TOM_SA_LABOR(1)` = 1 × 2.5 × 36 × 1.10 = 99 hours.
5. **No error cells:** no `#REF!`, `#DIV/0!`, `#NAME?` or other errors anywhere, including at 0 beds.
6. **Formulas, not values:** every calculated cell is a formula referencing the named inputs; no typed-in numbers outside the Inputs sheet. Allowed exceptions: the three bed counts (Solver changes them) and the bed-number column (1, 2, 3…) in the marginal-cost tables.
7. **Solver:** objective maximize `PROFIT`; changing cells `TOM_BEDS`, `CAR_BEDS`, `MES_BEDS`; method GRG Nonlinear; constraints: beds are integers, `0 ≤ TOM_BEDS ≤ TOM_CAP`, `0 ≤ CAR_BEDS ≤ CAR_CAP`, `0 ≤ MES_BEDS ≤ MES_CAP`, `TOM_BEDS + CAR_BEDS + MES_BEDS ≤ TOTAL_BEDS`, `TEMPS_NEEDED ≤ MAX_TEMPS`. Every constraint-check cell on the Checks sheet shows green (pass).

## Outputs

All outputs appear on the Summary sheet:

- Best mix: `TOM_BEDS`, `CAR_BEDS`, `MES_BEDS`
- Season profit, revenue, labor cost, fertilizer cost: `PROFIT`, `REVENUE`, `LABOR_COST`, `FERT_COST`
- Total labor hours, my hours, temp hours, temps needed: `TOTAL_LABOR`, `OWN_USED`, `TEMP_LABOR`, `TEMPS_NEEDED`
- Profit per crop: `TOM_PROFIT`, `CAR_PROFIT`, `MES_PROFIT`
- Crossing bed for each crop: `TOM_CROSS`, `CAR_CROSS`, `MES_CROSS`
- Blended labor rate: `BLENDED_RATE`

## Audit findings

Audited 2026-10-16 in desktop Excel with Solver.

| # | Check | What I found | What I did | What it would have caught |
|---|---|---|---|---|
| 1 | q = 1 by hand (tomatoes) | First tomato row shows 99.0 labor hours; hand calculation 1 × 2.5 × 36 × 1.10 = 99.0. Match. | None needed | A dropped `(1 + DIM)^q` exponent, the most common structural defect |
| 2 | Cross-check against the Farm Profit Lab | Tomato bed 5: marginal labor cost $6,780.43 in both the Lab and my model (my MC column shows $7,660 because it also includes $880 fertilizer). No discrepancy. | None needed | Errors in the middle of the model, checked against an independent implementation |
| 3 | Solver from 0/0/0 | Optimal mix 10 tomato / 20 carrot / 30 mesclun, season profit $42,775 | Recorded | A wrong optimum or broken objective |
| 4 | Solver from 20/0/0 | Same mix (10 / 20 / 30) and same profit ($42,775). No path dependence observed from these two starting points. | Recorded | A local peak mistaken for the answer (GRG Nonlinear stops at the first peak) |
| 5 | Check figures | Mix 10 / 20 / 30 matches. Profit $42,775 vs published $42,762: $13 off, within my ±$25 tolerance, so check 7 passes. Standalone crossings 10 / 10 / 6 match ~10 / ~10 / ~6. | Kept the tolerance; the $13 is consistent with rounding in `CAR_HRS` = 0.833 | Everything else, in aggregate |
| 6 | Checks sheet after Solver | All seven checks show PASS in green | None needed | A plan that breaks a cap, the 64-bed limit, the 4-temp limit or whole beds |
| 7 | Formulas, not pasted values | Spot-checked yellow calculated cells; each contains a formula referencing named inputs, no hardcoded numbers | None needed | A number that is right now but wrong once an input changes |

**Deviation from spec (accepted):** inside the marginal-cost tables, formulas reference the row's bed number and the previous row's cost by cell address (e.g. `=C8-C7`) instead of pure named-range notation. Excel names cannot take a row argument, so this was accepted as functional and noted here as a minor deviation.

**Noted for Stage 3 (not explained here):** tomato marginal cost dips at bed 6 ($7,660 at bed 5, $4,906 at bed 6) before rising again.
