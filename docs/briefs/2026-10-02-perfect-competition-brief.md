---
type: brief
engagement: perfect-competition
capability: marginal-analysis
date: 2026-10-02
status: committed           # committed | superseded
hypothesis: "I predict the optimal mix will be 10 tomato beds, 20 carrot beds, and 30 mesclun beds, because tomatoes yield the highest revenue per bed, but labor penalties after 10 beds cause mesclun and carrots to deliver higher net profit per additional bed"
---

# Perfect Competition — engagement brief

## The problem

The problem or dilemma that is face is figuring out our source of produce for this upcoming season. The three options that we can plant would be tomatoe, carrot and or mesclun beds all three combined can be no more than 70 beds total, but our max or what we only have is 64 so we can't max out every crop. After a selection to be made in what we decided to harvest we are also not allowed to go back and change which means we would have to absorb any environmental factors that may impact the season and its growth. So we must come up with a solution of what mix of crop bed types will produce the highest marginal return when accounting for revenue per bed, the amount of work/field hours a week per bed, fertilizer and its associated diminishing returns.

Ultimately what the farm is trying to do is to find the correct mix of beds that will create the best profit in terms of the whole season. So what we care about most is the best mix of beds that overly produces or at least breaks even in terms of labor costs, and revenue from sales. We are looking to add beds as long as each new one breaks even or ends up creating a positive slope of financial gain recorded as profit.

Price per bed:

- Tomatoes: $8,800
- Carrots: $2,094
- Mesclun: $2,700

## What I am assuming

The season in total length will be 36 weeks long with fixed costs of 20,000 for the season. The best available across all crops selection is 64 beds with a total of 720 hours to be estimated/planned for the season. We are also able to utilize temp workers up to 4, at $17.36/hr, 1440 hrs each. Equation to be used to determine labor hours for q beds of one crop is `Labor(q) = q × hrs/wk/bed × 36 × (1 + dim)^q`. Extra labor is priced at the temp rate of $17.36/hr.

## Hypothesis

The hypothesis I would "I predict the optimal mix will be 10 tomato beds, 20 carrot beds, and 30 mesclun beds, because tomatoes yield the highest revenue per bed, but labor penalties after 10 beds cause mesclun and carrots to deliver higher net profit per additional bed".

**Why 60 beds instead of 64:** As you plant more beds, diminishing returns or labor penalties kick in. If planting the 61st through 64th beds costs more in extra labor or inputs than the extra revenue those 4 beds bring in, the marginal profit is negative. A profit-maximizing farm will stop planting before utilizing all 64 beds

The last four beds stay empty because we have an inverse effect when it comes to planting tomato beds. After the 10th bed, the 11th tomato bed gives us a negative gain when it comes to profit, and carrots and mesclun are already at their caps, which makes sense to stop tomatoes at 10 and leave the 4 beds empty.

**The numbers behind it** (case labor formula, extra labor at $17.36/hr; earnings = price minus fertilizer):

| Bed | Extra labor hours | Extra labor cost | Earns | Result |
|---|---|---|---|---|
| 10th tomato | 424 | $7,368 | $7,920 | makes money |
| 11th tomato | 490 | $8,510 | $7,920 | loses money |
| 20th carrot (cap) | 72 | $1,248 | $1,654 | makes money |
| 30th mesclun (cap) | 89 | $1,540 | $1,820 | makes money |

Total labor for 10 / 20 / 30: about 5,277 hours of the 6,480 available (720 own + 4 temps × 1,440).

## How I would know I was wrong

If the model's best plan has more than 10 tomato beds, or fewer than 30 mesclun beds, I was wrong.

If the model shows that planting all 64 beds is the most profitable mix, my 60-bed claim fails.
