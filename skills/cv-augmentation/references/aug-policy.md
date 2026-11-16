# Augmentation policy notes

## Order of operations

1. Fix labels and splits (`cv-data-quality`).
2. Skeleton + overfit batch with **no** aug (`train-verify`).
3. Overfit full train with capacity (still mild/no aug if needed).
4. Add geometric → photometric → cutout/mix — **one change per experiment** when diagnosing.
5. Re-check critical slices after each family lands.

## Task constraints (examples)

| Domain | Default caution |
|--------|-----------------|
| OCR / documents | Horizontal flip usually wrong; 90° only if labels rotate |
| Medical laterality | L/R flip can invert diagnosis |
| Traffic / driving | Vertical flip almost never; some rotations odd |
| Aerial / satellite | Rotations often OK; watch north-up products |
| Detection / seg | Every geometric transform updates boxes/masks/keypoints |

## Mixup / CutMix / Mosaic

- Great regularizers for classification and some detection recipes.
- Confirm loss/label encoding matches soft or multi-object targets.
- Keep a clean unaugmented val; don’t compare mixup-train metrics to mixup-val by accident.

## Video frames

- Prefer augs that preserve temporal identity when training clip models (same geometric params across a clip).
- For independent frame models, still avoid train/val leakage of near-dup frames before heavy aug hides it.
