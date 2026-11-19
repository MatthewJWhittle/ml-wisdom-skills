# Things to watch

Shared failure modes for ML/DL judgment skills. Use as a scan list when a gate fails or a result looks “too good.”

## Data

- Label noise, near-duplicates, train/val leakage (same patient, same scene, temporal bleed)
- Class imbalance mistaken for model failure (or success)
- Preprocessing that a human would never use to decide (broken crops, silent NaNs, inverted masks)
- Looking at raw files but never at **tensors that enter the model**

## Training correctness

- Loss at init wildly off theory (wrong reduction, logit scale, class weights)
- Augmentation on during a correctness gate
- Unfixed seeds when claiming reproducibility
- “Training” that never decreases train loss
- Tiny-batch overfit that fails → bug; that succeeds → permission to scale

## Experiment process

- Two changes at once (architecture + LR + aug) with no attribution
- Making a comparison without a credible baseline for the decision
- Tuning before the feedback map exists
- Primary signal up while a guardrail (slice, VRAM, leak symptom) collapses

## Feedback & loops

- Metrics too coarse to discriminate keep/discard
- Plots/scripts missing for the modality (e.g. CV with numbers only)
- Chat history as the only experiment log
- Soft gates with no pass criteria and no human waiver path

## Score looks good, use still fails

- Main score (Dice/IoU/accuracy) up, but **edges** or human “good enough” overlays fail
- Train with soft scores; deploy a hard mask / different threshold without saying so
- **mAP** up, but precision/recall at the **real deploy threshold** down
- Each **tile/crop** looks fine; the **stitched map**, track, or full page fails
- Neighboring tiles disagree on land cover that should be continuous (seams)
- Labels too coarse for what the product needs
- Eval tiles/norm/stitch ≠ how you actually serve
