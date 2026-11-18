# Metrics, slices, calibration

## Choosing a primary

Ask: what action changes if the metric moves?

| Decision style | Lean toward |
|----------------|-------------|
| Ranking / retrieval | AUC, NDCG, recall@k |
| Fixed threshold deploy | Precision/recall or cost at that threshold |
| Probabilities billed as confidence | Calibration-aware metrics + proper scoring |
| Balanced multi-class | Macro-F1 or balanced error — not micro-acc alone |
| Segmentation / detection | Task-standard IoU/AP **plus** slice audits |
| Boundary-critical seg | Overlap (Dice/IoU) **paired with** boundary/surface metric or band IoU — overlap alone under-penalizes soft edges |
| Deployed detector | Operating-point PR / cost **and** optional AP — not AP alone |
| Tiled / stitched dense pred | Chip metrics **and** mosaic/AOI or seam disagreement guardrail |

If stakeholders optimize a vanity metric, keep it as a **reported** number and put
the decision metric in the primary slot — or get a waiver.

**Metric–utility gap:** if the action cares about edges, rare classes, calibrated
scores, or a composed artifact, a global Dice/mAP/accuracy win is insufficient
without matching guardrails ([ADR 0010](../../../docs/adr/0010-metric-utility-gap.md)).

## Guardrail examples

- Per-class / per-site recall floors
- Max acceptable calibration error or Brier degradation
- Latency / VRAM / cost ceilings
- “No slice worse than X relative to baseline”
- Boundary / HD95 / surface Dice / edge-band IoU when contours matter
- Operating-point precision/recall floor (detection)
- Mosaic- or AOI-level score; overlap-strip class-disagreement rate (tiled EO/CV)
- Intended-use rubric fail rate on a fixed N-case board

## Slice checklist

For each release or loop milestone:

1. List slices that would make a global win unacceptable if they failed.
2. Ensure the eval command actually emits them (feedback map gap → propose in
   `AGENTS-FEEDBACK.md`).
3. Separate **product-critical** slices from exploratory ones.

## Leakage patterns (eval → train)

- Identical or near-duplicate samples across splits
- Early stopping + model selection + final claim all on one val set with no holdout
- Preprocessing fit (scaler, vocabulary, aug stats) on full data including test
- Label leakage via metadata correlated with the target only in the eval set

## Reporting

- State n, split id/hash, and eval command.
- Prefer intervals or repeated-seed spread when n is small; otherwise one honest
  full-set number beats false precision.
- Never round a debug-set 0.8731 into a headline without re-running full eval.
