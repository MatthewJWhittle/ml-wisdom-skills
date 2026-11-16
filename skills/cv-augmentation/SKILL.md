---
name: cv-augmentation
description: Judges CV augmentation policy as regularization after correctness gates — geometric vs color vs cutout/mixup, task constraints, train/serve consistency. Use when adding or tuning image/video-frame augs, debugging overfit-batch failures with augs on, or aligning train preprocessing with serving.
license: MIT
---

# CV augmentation

**Overlay** on `train-verify` (and `data-judgment` when labels interact with geometry). Load the spine skills for overfit-batch / loss@init gates; this skill for *what* to augment and *when*. Compose with Albumentations/torchvision/Ultralytics configs — judgment here, CLI flags there.

## Policy (Karpathy recipe)

1. **Correctness first** — augs are regularization. Pass `train-verify` gates **with augmentation off** (overfit tiny batch, visualize-before-net).
2. **Disable for overfit-batch** — dropout, weight decay, and augs off until the batch memorizes.
3. **Then regularize** — once the model can overfit train, add augs (and other regularizers) one family at a time.
4. **Visualize after enable** — decode the post-aug batch with labels transformed; catch flip/box bugs early.

## Decision tree

```
train-verify failed with augs ON?
  → turn augs OFF; re-run gates; fix pipeline/labels

gates green, train overfits, val weak?
  → add augs as regularization (not as a substitute for more real data)

val gaps look like domain/lighting/pose?
  → prefer augs that mimic that variation (see families below)

task has asymmetries (text, laterality, medical L/R)?
  → ban or constrain geometric flips/rotations
```

## Families

| Family | Typical use | Watch |
|--------|-------------|--------|
| **Geometric** | Flip, rotate, scale, crop, affine | Label transform must match; don’t flip text/medical asymmetries blindly |
| **Color / photometric** | Brightness, contrast, HSV, JPEG noise | Match sensor/domain; extreme color can erase signal |
| **Occlusion** | Cutout, random erasing | Boxes/masks still valid; don’t erase the only object always |
| **Mixing** | Mixup, CutMix, Mosaic | Soft labels / multi-instance semantics; eval usually unaugmented |

Depth: [references/aug-policy.md](references/aug-policy.md).

## Train / serve consistency

- **Train** may augment; **eval/serve** use deterministic resize/normalize only (unless TTA is an explicit product choice).
- Same resize, crop policy (center vs random), color order, and norm stats as training *without* stochastic augs.
- Document the contract in the feedback map; mismatch is a silent accuracy killer ([things-to-watch](../../references/things-to-watch.md)).

## Done when

- Aug off for verify gates; aug on only after overfit capacity exists.
- Task constraints written (allowed / forbidden transforms).
- One post-aug visualization artifact exists and was reviewed.

## Out of scope

Dataset label/leakage QA → `cv-data-quality`. Backbone/pretrain → `cv-transfer`.
