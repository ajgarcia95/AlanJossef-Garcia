---
type: spec
capability: marginal-analysis
engagement: perfect-competition
date: 2026-10-16
status: draft               # draft | built | audited
built_by:                   # AI tool (or "manual") that generated model.xlsx
---

# Perfect Competition — model spec

<!--
Stage 2 rules (from the course guide):
- You write this spec; AI may critique it for gaps but may not write it.
- Named-range notation only (e.g. TOM_PRICE), never cell addresses.
- Test: someone who has never seen the case could build the workbook from this file alone.
- Commit this BEFORE model.xlsx exists. Commit order is graded.
- If you explain anything in chat during the build, add it here, commit, and regenerate.
Delete this comment block when done.
-->

## Purpose

<!-- Two sentences: the decision this model supports and what it must be able to answer. -->

The farm is deciding on what combination of beds will ultimately produce the best profit. This is determined through a combination of things like labor costs, and revenue from each style bed. The finished model should show us the best mix, overall profit/revenue with the use of properly executed man hours through our own labor as well as hiring temps at a set rate.

## Inputs

<!-- Every input as a named contract. Choose names and use them the same way everywhere. -->

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

## Structure

<!-- Each sheet or region and its purpose: inputs, cost structure, marginal-cost schedules, optimization, checks. -->

The workbook has five parts, one sheet each:

1. **Inputs**: the 24 named case numbers from the table above, so nothing is typed into formulas.
2. **Cost structure**: labor hours and costs for the chosen mix.
3. **Marginal-cost schedules**: the cost of each extra bed, for each crop.
4. **Optimization**: the three bed counts Solver changes, and the profit it maximizes.
5. **Checks**: pass/fail cells, such as whether beds are 64 or fewer and temps are 4 or fewer.

Color coding:

- Inputs stay plain, with no color.
- Each crop has its own color wherever crop-specific numbers appear (including the marginal-cost schedules): tomatoes red, carrots orange, mesclun green.
- Every formula cell is highlighted light yellow, meaning it should not be touched unless needed.

## Calculation logic

<!-- Formulas in named-range notation. The labor function must include the (1 + DIM)^q term. -->

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

Marginal cost of the 5th tomato bed is the additional total cost the farm incurs by planting 5 beds of tomatoes instead of 4. Labor plus fertilizer go into it; fixed costs do not. Calculating a crop's schedule as a "standalone" curve means evaluating that crop in complete isolation (e.g., setting Carrot Beds = 0 and Mesclun Beds = 0), so my 720 hours go to that crop first.

## Conventions

<!-- Rules not visible in the formulas: farmer's 720 hrs used before temps; P&L labor at the blended rate
     (total labor $ / total hours); how temp hours are paid; rounding; what happens at q = 0 and at caps. -->

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

## Validation rules

<!-- Acceptance criteria, written before the build:
     - Check figures: optimal mix 10 / 20 / 30 (60 beds); season profit $42,762; standalone P ≈ MC crossings ~10 / ~10 / ~6
     - q = 1 hand calculation (tomatoes: 1 × 2.5 × 36 × 1.10 = 99 hrs)
     - No error cells; every calculated cell contains a formula
     - Solver: maximize profit, GRG Nonlinear, integer bed counts, caps, 64 beds, temps ≤ 4; all constraint checks green -->

## Outputs

<!-- Each result the model must report, by name. -->

## Audit findings

<!-- Added AFTER the build. For each check (at least 3): what I checked, what I found, what I did,
     and what it would have caught. Include Solver from 0/0/0 and 20/0/0, and the Farm Profit Lab cross-check.
     Note where the tomato MC dip appears (near 6 beds) without explaining it. -->
