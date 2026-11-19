# Initial-loss expectations

Use the actual logits and target contract to check loss before the first update. Account for masks, weighting, smoothing, reduction and auxiliary terms. Inspect components separately for composite losses.

| Objective | Useful reference |
|-----------|------------------|
| Multiclass cross-entropy | Equal logits give `ln(C)` per valid example with normalized targets and ordinary unweighted mean reduction. Class balance is irrelevant for equal logits. Prior-biased or widely spread logits change that expectation. |
| Binary or multilabel BCE with logits | Zero logits give `ln(2)` per entry before weighting. Positive weighting and sum reduction change the reported value. |
| Regression | Compare a constant prediction from the training mean for MSE or median for MAE. Compare on the same batch and reduction, and inspect target scaling. |
| Detection or dense prediction | Check each loss term against actual valid elements, anchors or pixels and its denominator. Inspect a small hand-computable case when the full expression is awkward. |
| Next-token prediction | Uniform logits give `ln(V)` per unmasked token under ordinary mean NLL, where `V` is vocabulary size. Use the actual logits for pretrained heads. |

## Two interpretation checks

- A network can memorize fixed noise assigned to training examples. If held-out performance matches the input-independent baseline, high training accuracy alone gives no evidence of leakage. If held-out skill persists, inspect group overlap and any target information that survived the control.
- A binary model with 90% positives and logits initialized to the label prior has mean BCE near 0.325 on a representative batch, below the zero-logit baseline of 0.693. A tiny batch with smoothed labels also has a positive cross-entropy floor even at the optimum.
