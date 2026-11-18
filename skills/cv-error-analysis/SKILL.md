---
name: cv-error-analysis
description: >-
  Diagnoses CV model errors when metrics plateau or look fine but outputs fail
  intended use — confusion matrices, hard examples, size/occlusion/scene slices,
  FP/FN grids, soft-boundary and tile-mosaic/seam failures — then attributes
  data vs model vs aug vs threshold. Use after a trusted eval, before starting
  a run-objective-loop on vision tasks, or when Dice/mAP/accuracy disagree with
  overlays or stitched products.
license: MIT
---

# cv-error-analysis

**Overlay** on `validate-results` (and `diagnose-training` when training dynamics look sick). Load the spines for metric trust and training pathology; this skill for *vision* failure attribution. Hand off to `run-objective-loop` **only after** a written diagnosis — loops without diagnosis thrash augs and LRs.

Compose with plotting/eval scripts from the feedback map; this skill decides *what* to look at and *what it means*. Doctrine: [ADR 0010](../../docs/adr/0010-metric-utility-gap.md).

## Preconditions

- Eval pipeline trusted (`validate-results` gates): fixed split, correct decode, no train/serve mismatch.
- Prefer unaugmented val/test for diagnosis (TTA is a separate experiment).
- If training is sick (NaN, can’t overfit), stop — `diagnose-training` first.

## Procedure

1. **Global picture** — confusion matrix (classification) or TP/FP/FN counts by class (detection/seg).
2. **Metric–utility check** — does the primary celebrate mass/overlap/AP while the product needs boundaries, operating-point PR, or a **composed** artifact (mosaic, track)?
3. **Qualitative grids** — fixed-size boards of high-confidence FP, FN, correct-but-hard TP; overlays for boxes/masks; include **“score OK / look bad”** cases when suspected.
4. **Slice** — stratify by object size, occlusion, density, scene/time-of-day, sensor, near-dup clusters; for seg add **boundary band**; for tiled products add **seam / adjacent-tile** strips.
5. **Attribute** — each dominant error mode → data / model / aug / threshold / **composition** (table below).
6. **One next lever** — only then open `run-objective-loop` with that lever in the mutable surface.

Templates: [references/error-grids.md](references/error-grids.md).

## Attribution table

| Evidence | Lean toward |
|----------|-------------|
| Humans disagree or labels wrong on FN/FP grids | **Data** → `cv-data-quality` |
| Labels too coarse for the utility (e.g. need crisp parcel edge, GT is blob) | **Data** / metric contract → `validate-results` |
| Near-dups / leakage inflate val | **Data** (split) |
| Soft/blobby edges; Dice fine; surface/boundary metric or eye test fails | **Model** / **loss** / threshold — diagnose first; one lever only |
| Tiles look fine; mosaic seams or adjacent-tile class flips | **Composition** — stitch/overlap/norm/context; eval at mosaic scale |
| Train overfit OK, specific rare slice fails | **Data** (collect/rebalance) or **model** capacity/representation |
| Errors match banned/missing transforms (e.g. never saw rotation) | **Aug** → `cv-augmentation` |
| Precision/recall trade only; curves look sane | **Threshold** / calibration |
| All slices mediocre; backbone undertrained or domain gap | **Model** / **transfer** → `cv-transfer` |
| Loss/NaN/can’t overfit | Stop — `diagnose-training`, not error grids |

Guardrails: [things-to-watch](../../references/things-to-watch.md).

## Done when

- Top error modes listed with example IDs and an attributed cause each.
- If metric–utility or composition mismatch: named explicitly (not “need more epochs”).
- Explicit statement: ready for `run-objective-loop` **or** blocked on data/label/feedback work.
- No multi-lever “kitchen sink” experiment proposed as the first fix.

## Out of scope

Wiring new metrics from scratch → `wire-feedback`. Blind hyperparam ratchet → `run-objective-loop` after this skill. Reimplementing tiling/stitch libraries → compose with project/geo tooling.
