---
name: cv-error-analysis
description: >-
  Diagnoses vision model failures when metrics plateau or look fine but outputs
  fail real use — FP/FN grids, soft boundaries, tile seams / mosaics, slices —
  then attributes data vs model vs aug vs threshold. Use after a trusted eval,
  before a vision run-objective-loop, or when Dice/mAP/accuracy disagree with
  overlays or stitched maps.
license: MIT
---

# cv-error-analysis

**Overlay** on `validate-results`. Also load `diagnose-training` if training
itself looks broken. This skill answers: *what kind of vision failure is this?*

Hand off to `run-objective-loop` **only after** a short written diagnosis —
otherwise agents thrash augs and learning rates.

Use plot/eval commands from the feedback map. Background:
[ADR 0010](../../docs/adr/0010-metric-utility-gap.md).

## Before you start

- Eval setup is trustworthy (`validate-results`): fixed split, correct decode,
  train/serve match.
- Prefer unaugmented val/test for diagnosis (TTA is a separate experiment).
- If loss is NaN or you cannot overfit a tiny batch → `diagnose-training` first.

## Steps

1. **Overview** — confusion matrix (classification) or TP/FP/FN by class
   (detection/seg).
2. **Does the score match the job?** — e.g. Dice loves overlap; the product
   needs sharp edges. Or each tile scores well but the **stitched map** fails.
3. **Look at grids** — FP, FN, hard TP; boxes/masks overlaid. Include cases
   where the **score is high but the picture looks wrong**.
4. **Slice** — size, occlusion, scene, sensor, … For segmentation, check a
   **boundary strip**. For tiled products, check **seams between tiles**.
5. **Attribute** each top failure mode (table below).
6. **One next change** — then open `run-objective-loop` with only that lever.

Grid templates: [references/error-grids.md](references/error-grids.md).

## What evidence points to

| If you see… | Likely cause |
|-------------|--------------|
| Humans disagree or labels wrong on the grids | **Data** → `cv-data-quality` |
| Need crisp edges / fine classes, but labels are coarse blobs | **Data / metric choice** → `validate-results` |
| Near-dups leak across splits | **Data** (split) |
| Soft edges; Dice fine; eye or boundary metric fails | **Model / loss / threshold** — one change only |
| Tiles OK; mosaic seams or neighboring tiles disagree on continuous land cover | **Whole-product / stitch** — check overlap, norm, context; eval the mosaic |
| Train fine; one rare slice fails | **Data** or **model** capacity |
| Failures match missing augs (e.g. never rotated) | **Aug** → `cv-augmentation` |
| Only a precision/recall trade at threshold | **Threshold** |
| Everything mediocre; domain gap | **Model / transfer** → `cv-transfer` |
| NaN / cannot overfit | Stop — `diagnose-training` |

Also: [things-to-watch](../../references/things-to-watch.md).

## Done when

- Top failure modes listed with example ids and a cause each.
- If “score good / use bad” or “tiles good / mosaic bad”: say so plainly
  (not “need more epochs”).
- Clear: ready for `run-objective-loop` **or** blocked on data/labels/feedback.
- First fix is **one** lever, not a kitchen sink.

## Out of scope

Adding new metrics wiring → `wire-feedback`. Blind hyperparameter search →
`run-objective-loop` after this. Rebuilding tiling libraries → use the
project’s geo/CV tools.
