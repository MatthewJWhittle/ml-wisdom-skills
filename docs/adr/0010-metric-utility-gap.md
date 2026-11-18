# When the score looks good but the model is bad for real use

Background research: [`docs/research/2026-11-18-cv-defect-diagnosis-beyond-metrics.md`](../research/2026-11-18-cv-defect-diagnosis-beyond-metrics.md).

## Decision

Treat “the metric looks fine, but the model fails what we actually need” as a first-class failure.

Two common shapes:

1. **Wrong score for the job** — e.g. Dice/IoU cares about overlap mass; the product needs sharp boundaries.
2. **Score the part, ship the whole** — e.g. each tile looks fine; the stitched map has seams or class flips.

Same idea shows up elsewhere: mAP up but the real threshold fails; per-image OK but per-field fails; labels too coarse for the product; val inflated by leakage.

## What skills should do

1. **Say what decision the model supports**, then pick primary and guardrail metrics from that (`validate-results`, `design-experiment`).
2. **Wire ways to see that decision** — prediction overlays, boundary checks, mosaic/AOI eval, threshold PR, a short “good enough” rubric (`wire-feedback`).
3. **Do not keep a run** if the main score improves but those checks fail (`run-objective-loop`, `cv-error-analysis`).
4. **Add detail in overlays**, not new tool encyclopedias. Do not rebuild MONAI / Ultralytics / torchgeo docs here.

## Non-goals

- Blocking all work just because viz is missing (warn and recommend).
- Shipping a separate geo-tiling skill in this change — refine existing skills first.
