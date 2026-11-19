# Init loss notes

Check the loss before the first update. Use the intended head and target contract, including weights, masking, smoothing and reduction. For custom or composite objectives, check individual terms on a small example with an independently calculated expectation.

## Classification (softmax CE)

- Equal logits across `C` classes give `ln(C)` per valid example with normalized targets and ordinary unweighted mean reduction. Class balance alone does not determine this value. Prior-biased or widely spread logits change it.
- For an unexpected loss, inspect logit scale, class weights, targets, and sum versus mean reduction. For an unexpectedly low loss, also check whether a pretrained or already-updated checkpoint was loaded.

## Binary BCE with logits

- Zero logits give `ln(2)` ≈ 0.693 per entry before weighting. Check positive weighting, masking and which entries the reduction averages.
- With 90% positives and predictions initialized to that prior, mean unweighted BCE on a batch with the same prevalence is about 0.325. This is consistent with the intended head initialization.

## Regression (MSE / L1)

- Compare to a constant prediction based on the training mean for MSE or median for MAE. Score on the same batch with the same reduction. Inspect target scaling if initial error is unexpectedly high.

## Detection / dense tasks

- Check each term against the valid elements, anchors or pixels and the denominator used. Compare with a small hand-computable case or a known-good run with the same objective and settings.

## Generative LM

- Uniform logits give `ln(V)` per unmasked token for ordinary mean negative log-likelihood, where `V` is vocabulary size. For pretrained heads, use the actual logits.
