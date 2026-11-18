# Error grids and slices

## Minimum boards

| Board | What to show |
|-------|----------------|
| High-conf FP | Model sure, label says no / wrong box |
| Misses (FN) | Label yes, model missed or IoU failed |
| Hard TP | Correct but low margin / heavy occlusion |
| Confusion pairs | Top confused class pairs side by side |
| Score high / looks wrong | High Dice/IoU/AP but overlay fails the real job (soft edge, bad seam, wrong threshold) |

Always show **image + label + prediction** together.

## Useful slices (pick what fits)

- Object **size** (small / medium / large)
- Extreme aspect ratios
- Occlusion / truncation
- Crowding (how many instances per image)
- Scene, camera, day
- Near-dup distance to train (leakage smell)
- **Boundary strip** (look or score only near the contour)
- **Seams between tiles** when you stitch a mosaic

## Detection / segmentation extras

- Per-class AP and PR before fiddling thresholds
- Wrong box vs wrong class
- Soft edges vs missed objects
- Soft training scores vs **hard mask at the deploy threshold**
- Tiled maps: look at the **mosaic**, not only chips — chip scores can lie

## Note for EXPERIMENTS.md

```text
mode: <short name>
count: <n>
examples: <ids>
cause: data | model | aug | threshold | whole-product
score_vs_use: match | mismatch
next_change: <one lever>
blocked_on: <none | labels | split | feedback | mosaic-eval>
```
