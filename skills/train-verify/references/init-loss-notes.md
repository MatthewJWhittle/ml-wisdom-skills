# Init loss notes

Quick expectations for `train-verify` gate 3. Custom losses need a human-stated target.

## Classification (softmax CE)

- Balanced C-way at random init: loss ≈ `ln(C)` = `-log(1/C)`.
- Far above: logit explosion, wrong reduction (`sum` vs `mean`), label smoothing surprise, class weights mis-applied.
- Far below: leak, wrong labels, evaluating train after accidental steps, or head initialized to priors too hard.

## Binary BCE with logits

- Around `ln(2)` ≈ 0.693 for balanced data at zero logits.

## Regression (MSE / L1)

- Compare to predicting train mean / median (baseline ladder). Init MSE much worse than mean-predictor → check target scaling.

## Detection / dense tasks

- Use the framework’s known init behavior (Ultralytics/HF tool skills). Prefer “finite, stable, not orders-of-magnitude off a fresh official recipe run” over a hand formula.

## Generative LM

- Init NLL should be near `-log(1/V)` for uniform-over-vocab (adjusted for BPE). Gate 5 (noise inputs) is often skipped with an explicit note.
