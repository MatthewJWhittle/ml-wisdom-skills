# CV visualization checklist

Run **before** model code and again on tensors **immediately before** `model(x)` (Karpathy: only source of truth).

## Grids to render

| View | What to look for |
|------|------------------|
| Random train batch | Corrupt decode, wrong labels, bizarre crops |
| Per-class grids | Collapsed/confused classes, junk dominant |
| Hard / rare classes | Mislabels, near-empty support |
| Largest / smallest images | Aspect/resolution pipeline bugs |
| Near-dup pairs across splits | Leakage |
| Boxes/masks overlaid | Geometry/convention errors |
| Same ID train vs val | Accidental copy |

## Video-as-frames

- Sample start / mid / end of clips; check temporal near-dups across splits.
- Confirm frame rate / stride isn’t silently duplicating content into both sides.

## Record

For each issue class: count, severity, example paths, fix or waiver. Link paths from the feedback map.
