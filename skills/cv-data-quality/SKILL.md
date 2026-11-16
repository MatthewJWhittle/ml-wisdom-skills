---
name: cv-data-quality
description: Judges image/video-frame dataset quality for CV — label errors, near-duplicates, corrupt media, class confusion, bbox/mask quality, train/val leakage, resolution quirks. Use when inspecting a vision dataset, before training, after suspicious metrics, or when near-dup leakage or annotation bugs are suspected.
license: MIT
---

# CV data quality

**Overlay** on `data-judgment`. Load **both**: spine for splits, sampling, and label-process gates; this skill for vision-specific failure modes. Compose with tool skills (datasets CLIs, YOLO/label viewers) — do not re-document them.

Applies to still images and short video treated as frames. Goal: *become one with the data* before touching the net ([Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/); CS231n data hygiene).

## Gates (pass or human waiver)

1. **Visualize before the net** — random and stratified grids of decoded pixels + labels (class, bbox, mask) as the model will see them.
2. **No silent corruptions** — unreadable files, zero-byte, truncated JPEG/PNG, all-black/all-white, extreme aspect outliers accounted for.
3. **Leakage check** — near-duplicate images (or frames from the same clip) do not straddle train/val/test.
4. **Annotation sanity** — sample of boxes/masks/keypoints reviewed for emptiness, flipped axes, class swaps, and off-image geometry.

## Checklist

### Labels & classes
- Spot-check per class; hunt **class confusion** pairs humans also mix up.
- Class imbalance vs. true prior; rare classes that are mostly junk/noise.
- Labeler disagreement / double-annotation on a slice when stakes are high.

### Near-duplicates & leakage
- Hash / embedding / perceptual-near-dup search **across splits**.
- Video: treat consecutive frames and same-scene bursts as near-dups unless the split is deliberately temporal.
- Same asset re-exported at different resolution still counts as leakage.

### Corrupt / quirky media
- Decode failures; EXIF orientation ignored vs applied inconsistently.
- Resolution and aspect: tiny thumbs, huge panoramas, letterboxing baked into pixels.
- Color space / 16-bit / grayscale misread as RGB.

### Boxes, masks, keypoints
- Empty or near-empty boxes; boxes covering the whole image by mistake.
- Masks that don’t match the box; inverted masks; polygon self-intersections.
- Coordinate convention bugs (xyxy vs xywh, normalized vs absolute, y-down).

### Visualization pass (minimum)
See [references/visualization-checklist.md](references/visualization-checklist.md). Prefer wiring these into the feedback map (`AGENTS-FEEDBACK.md`) so later skills can re-run them.

## Done when

- Documented findings (counts + examples) and either fixes or explicit waivers.
- Train/val/test near-dup policy stated.
- Spine `data-judgment` split/label gates still satisfy.

## Out of scope

Augmentation policy → `cv-augmentation`. Transfer/backbone choice → `cv-transfer`. Error grids after training → `cv-error-analysis`.
