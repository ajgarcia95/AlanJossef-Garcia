# Perfect Competition case — the givens

Source: https://adamwstauffer.github.io/ai-lms/case-perfect-competition.html#givens
Transcribed 2026-10-02. Verify every figure against the source before using it in a model.

| Crop     | Bed cap | Revenue / bed | Field hrs / wk / bed | Fertilizer / bed | Diminishing-returns rate |
|----------|---------|---------------|----------------------|------------------|--------------------------|
| Tomatoes | 20      | $8,800        | 2.5                  | $880             | 10%                      |
| Carrots  | 20      | $2,094        | 0.833                | $440             | 2.5%                     |
| Mesclun  | 30      | $2,700        | 1.25                 | $880             | 1.25%                    |

Farm parameters

- Season: 36 weeks
- Fixed costs: $20,000 for the season
- Beds available: 64 (bed caps sum to 70)
- Own labor: 720 hrs at $34.72/hr
- Temporary workers: up to 4, at $17.36/hr, 1,440 hrs each
- Labor formula: `Labor(q) = q × hrs/wk/bed × 36 × (1 + dim)^q`
- Decision rule the case is built on: keep adding beds while the next bed earns more than it costs (P = MC).
