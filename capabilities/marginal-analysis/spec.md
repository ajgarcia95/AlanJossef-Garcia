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

## Structure

<!-- Each sheet or region and its purpose: inputs, cost structure, marginal-cost schedules, optimization, checks. -->

## Calculation logic

<!-- Formulas in named-range notation. The labor function must include the (1 + DIM)^q term. -->

## Conventions

<!-- Rules not visible in the formulas: farmer's 720 hrs used before temps; P&L labor at the blended rate
     (total labor $ / total hours); how temp hours are paid; rounding; what happens at q = 0 and at caps. -->

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
