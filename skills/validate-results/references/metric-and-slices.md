# Metrics and slices

## Choosing a primary

Ask: **what action changes if this number moves?**

| Kind of decision | Prefer |
|------------------|--------|
| Ranking / retrieval | AUC, NDCG, recall@k |
| Fixed threshold in production | Precision/recall or cost **at that threshold** |
| Probabilities used as confidence | Calibration-aware scores |
| Imbalanced multi-class | Macro-F1 / balanced error — not accuracy alone |
| Segmentation / detection | Standard IoU/AP **plus** slices |
| Boundaries matter (organs, parcels, footprints) | Overlap (Dice/IoU) **and** a boundary/edge check — overlap alone forgives soft edges |
| Detection you ship at one score | Operating-point PR **and** optional AP — not AP alone |
| Parts combined into one output | Part scores **and** a check on the whole output or its boundaries |

If stakeholders love a vanity metric, report it — but put the **decision**
metric in the primary slot (or get a waiver).

**Score looks good, use still fails:** when the job cares about edges, rare
classes, real thresholds, or a combined product, a global Dice/mAP/accuracy
win is not enough without matching guardrails
([ADR 0010](../../../docs/adr/0010-metric-utility-gap.md)).

## Guardrail examples

- Per-class or per-site recall floors
- Calibration / confidence quality floors
- Latency / VRAM / cost ceilings
- “No critical slice worse than X vs baseline”
- Boundary / edge-band / surface distance when contours matter
- Precision/recall at the **deploy** threshold (detection)
- Whole-output score or cross-part consistency check (assembled documents, video tracks, stitched images)
- Fail rate on a short human “good enough” rubric

## Slice checklist

1. List slices that would make an overall win unacceptable if they failed.
2. Make sure the eval command actually prints them (gap → `AGENTS-FEEDBACK.md`).
3. Mark which slices are product-critical vs exploratory.

## Eval leaking into train

- Near-duplicate samples across splits
- Early stop + selection + final claim all on one val set with no holdout
- Scaler / vocab / aug stats fit on full data including test
- Metadata that leaks the label only in eval

## Reporting

- State n, split id, and the eval command.
- Prefer uncertainty when n is small; one honest full-set number beats fake precision.
- Do not promote a debug-subset score to a headline without full eval.
