---
name: cv-error-analysis
description: Diagnoses CV model errors via confusion matrices, hard examples, size/occlusion/scene slices, and FP/FN qualitative grids — then attributes data vs model vs aug vs threshold. Use after a trusted eval, when metrics plateau, or before starting a `run-objective-loop` on vision tasks.
license: MIT
---

# CV error analysis

**Overlay** on `validate-results` (and `diagnose-training` when training dynamics look sick). Load the spines for metric trust and training pathology; this skill for *vision* failure attribution. Hand off to `run-objective-loop` **only after** a written diagnosis — loops without diagnosis thrash augs and LRs.

Compose with plotting/eval scripts from the feedback map; this skill decides *what* to look at and *what it means*.

## Preconditions

- Eval pipeline trusted (`validate-results` gates): fixed split, correct decode, no train/serve mismatch.
- Prefer unaugmented val/test for diagnosis (TTA is a separate experiment).

## Procedure

1. **Global picture** — confusion matrix (classification) or TP/FP/FN counts by class (detection/seg).
2. **Qualitative grids** — fixed-size boards of high-confidence FP, FN, and correct-but-hard TP; overlays for boxes/masks.
3. **Slice** — stratify by object size, occlusion, density, scene/time-of-day, sensor, near-dup clusters.
4. **Attribute** — each dominant error mode → data / model / aug / threshold (table below).
5. **One next lever** — only then open `run-objective-loop` with that lever in the mutable surface.

Templates: [references/error-grids.md](references/error-grids.md).

## Attribution table

| Evidence | Lean toward |
|----------|-------------|
| Humans disagree or labels wrong on FN/FP grids | **Data** → `cv-data-quality` |
| Near-dups / leakage inflate val | **Data** (split) |
| Train overfit OK, specific rare slice fails | **Data** (collect/rebalance) or **model** capacity/representation |
| Errors match banned/missing transforms (e.g. never saw rotation) | **Aug** → `cv-augmentation` |
| Precision/recall trade only; curves look sane | **Threshold** / calibration |
| All slices mediocre; backbone undertrained or domain gap | **Model** / **transfer** → `cv-transfer` |
| Loss/NaN/can’t overfit | Stop — `diagnose-training`, not error grids |

Guardrails: [things-to-watch](../../references/things-to-watch.md).

## Done when

- Top error modes listed with example IDs and an attributed cause each.
- Explicit statement: ready for `run-objective-loop` **or** blocked on data/label work.
- No multi-lever “kitchen sink” experiment proposed as the first fix.

## Out of scope

Wiring new metrics from scratch → `wire-feedback`. Blind hyperparam ratchet → `run-objective-loop` after this skill.
