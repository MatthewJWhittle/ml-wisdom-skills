# Error grids & slices

## Minimum grids

| Grid | Content |
|------|---------|
| High-conf FP | Model sure, label negative / wrong box |
| FN (misses) | Label positive, model below threshold or IoU fail |
| Hard TP | Correct but low margin / heavy occlusion |
| Confusion pairs | Top off-diagonal class pairs side-by-side |

Always show **pixels + labels + predictions** on the same tile.

## Useful slices (pick what the domain implies)

- Object **area** quartiles (small/medium/large)
- Aspect ratio extremes
- Occlusion / truncation tags if present
- Crowding (instance count per image)
- Scene, camera, acquisition day
- Distance to nearest train near-dup (leakage smell)

## Detection / segmentation extras

- Per-class AP and PR curves before threshold fiddling
- Localization vs classification errors (right class, bad box vs wrong class)
- Boundary failures vs missed instances for masks

## Write-up schema (for EXPERIMENTS.md / reasoning trace)

``
mode: <short name>
count: <n>
examples: <ids>
attribution: data | model | aug | threshold
next_lever: <single change>
blocked_on: <none | labels | split | feedback>
``
