# Metric–utility gap (and composition mismatch) as core CV judgment

Approved doctrine for refining CV / eval skills. Detailed evidence: [`docs/research/2026-11-18-cv-defect-diagnosis-beyond-metrics.md`](../research/2026-11-18-cv-defect-diagnosis-beyond-metrics.md).

## Decision

Treat “metrics look good but the model is bad for use” as a first-class failure class: the **metric–utility gap**, often compounded by **composition mismatch** (score the part, ship the whole).

Teaching cases (Dice vs boundary fidelity; per-tile OK / mosaic seams in geo CV) are instances of a wider pattern: proxy mismatch, operating-point mismatch, taxonomy mismatch, unit-of-analysis mismatch, train-vs-serve contract skew, leakage-inflated scores, etc.

## Implications for the pack

1. **Name the decision first** — primary vs guardrail follow the product action, not the leaderboard default (`validate-results`, `design-experiment`).
2. **Wire observation channels that match the decision** — pred overlays, boundary/surface metrics, mosaic/AOI eval, operating-point PR, short intended-use rubrics (`wire-feedback` / feedback map).
3. **Veto keeps that fail utility or composition** — primary↑ is necessary, not sufficient (`run-objective-loop`, `cv-error-analysis`).
4. **Specialize checks in overlays, not new encyclopedias** — refine `validate-results`, `cv-error-analysis`, `wire-feedback`, and shared watchouts; optional thin geo tiling overlay later. Do not rebuild MONAI/Ultralytics/torchgeo how-tos.

## Non-goals

- Hard-blocking all work on missing viz (prefer warn + recommend until skills are refined).
- Shipping `cv-geo-tiling` / `design-loss` in this ADR — research and doctrine only; implementation is a follow-up.