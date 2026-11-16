# Data audit prompts

Questions to answer in writing during `audit-data`. Keep answers short.

## Human baseline

- If you had to label 20 examples by hand, what fields/pixels would you use?
- Would a human disagree often? On which slice?

## Integrity

- Can you find 3 mislabels in 20 minutes of spot-checking? If yes, estimate rate.
- Are duplicates identity-level (same file hash) or semantic (same scene, different crop)?
- What identifier defines an independent example (patient, farm, flight, session)?

## Model input

- Min/max/mean of the tensor batch — plausible for the declared normalization?
- For images: show a grid **after** training transforms with aug **off**.
- Any feature that is a deterministic function of the label or of split membership?

## Metrics foreshadow

- Which failure mode would top-1 / mAP / RMSE hide?
- What slice eval must exist before an objective loop (see feedback map gaps)?
