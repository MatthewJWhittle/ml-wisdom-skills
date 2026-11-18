# Error grids & slices

## Minimum grids

| Grid | Content |
|------|---------|
| High-conf FP | Model sure, label negative / wrong box |
| FN (misses) | Label positive, model below threshold or IoU fail |
| Hard TP | Correct but low margin / heavy occlusion |
| Confusion pairs | Top off-diagonal class pairs side-by-side |
| Score OK / look bad | High Dice/IoU/AP case where overlay fails **intended use** (soft edge, wrong seam, bad operating point) |

Always show **pixels + labels + predictions** on the same tile.

## Useful slices (pick what the domain implies)

- Object **area** quartiles (small/medium/large)
- Aspect ratio extremes
- Occlusion / truncation tags if present
- Crowding (instance count per image)
- Scene, camera, acquisition day
- Distance to nearest train near-dup (leakage smell)
- **Boundary band** (e.g. metrics or eye test inside an N-pixel contour strip)
- **Seam / adjacent-tile** strips when inference is tiled and stitched

## Detection / segmentation extras

- Per-class AP and PR curves before threshold fiddling
- Localization vs classification errors (right class, bad box vs wrong class)
- Boundary failures vs missed instances for masks
- Soft vs hard masks at **deploy threshold** (train may use soft Dice)
- For tiled EO/dense pred: mosaic board + overlap disagreement map — chip metrics alone can lie

## Write-up schema (for EXPERIMENTS.md / reasoning trace)

```text
mode: <short name>
count: <n>
examples: <ids>
attribution: data | model | aug | threshold | composition
metric_utility_gap: yes | no
next_lever: <single change>
blocked_on: <none | labels | split | feedback | stitch-eval>
```
