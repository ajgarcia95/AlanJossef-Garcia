# Prompt Log

Append as I work; never backfill or edit past rows. Newest at the bottom.

| Date | Tool | What I asked | What I got | What I did with it |
|---|---|---|---|---|
| 2026-09-15 | <!-- TODO(Alan) --> | | | |
| 2026-09-15 | <!-- TODO(Alan) --> | | | |
| 2026-09-29 | Claude | Apply review fixes to repo | AGENTS/CLAUDE/prompt-log scaffolds, folder READMEs, bio moved into README, .gitignore entries | <!-- TODO(Alan) --> |
| 2026-10-02 | Claude | Start Perfect Competition Stage 1 per the course guide | Empty brief scaffold (headings only), case givens copied to `data/` | <!-- TODO(Alan) --> |
| 2026-10-02 | Claude | Add a 64-bed falsification test to my brief | Suggested the sentence "If the model says planting all 64 beds is most profitable, your 60-bed claim fails"; added it in first person to "How I would know I was wrong" at my request (wording is Claude's, idea follows from my 60-bed reasoning) | Kept it in the brief |
| 2026-10-02 | Claude | Stage 1 critique of my committed brief (no rewrite, no replacement wording): name implicit assumptions, name unsupported claims, ask three client questions, is the hypothesis falsifiable? | 9 implicit assumptions (e.g. certain prices/yields, own-labor cost, temp pay and count); 8 unsupported claims (e.g. why the penalty starts at tomato bed 15, why mesclun stops at 25, fertilizer vs labor diminishing returns, feasibility within labor hours); 3 client questions (temps and cost, 16th tomato bed, profit and downside); falsifiable only partly: ROI test does not test the mix, 64-bed test misses 61-63 beds and a different 60-bed split | Revised after instructor feedback (2026-10-16): (1) Goal unclear: fixed, problem now says goal is best whole-season profit, adding beds while each breaks even or adds profit. (2) No prices: added the three prices per bed. (3) "Labor penalties after 15 beds" unsupported: 11th tomato bed costs $8,510 in labor at temp rate vs $7,920 earned, so revised tomatoes 15 → 10. (4) Why mesclun stops at 25: it shouldn't, every bed to the cap of 30 makes money, revised mesclun 25 → 30. (5) Mix didn't fit labor hours: new mix needs ~5,277 of 6,480 hrs. (6) Why 4 beds empty: carrots and mesclun at caps, 11th tomato bed loses money. (7) ROI test unsettleable: replaced with bed-count test (>10 tomatoes or <30 mesclun = wrong). (8) Other assumptions: priced extra labor at temp rate; number of temps left for Stage 2. (9) Kept: carrots at 20 and the all-64-beds test. |
| 2026-10-16 | Claude | Walk me through the instructor feedback one step at a time | Ran the case labor formula for tomato, carrot and mesclun beds at the temp rate (marginal hours and cost per bed, total hours for the mix); asked me questions for each fix; made a minimal edit to my "4 empty beds" sentence (15th/cap of 20 → 11th bed/stop at 10) and small grammar fixes; wrote the numbers table and the temp-rate assumption sentence | Used the numbers to revise to 10 / 20 / 30, wrote the goal sentence, chose the bed-count test |

## Errors caught

Where a model's output was wrong and how I caught it.

| Date | Tool | What was wrong | How I caught it |
|---|---|---|---|
