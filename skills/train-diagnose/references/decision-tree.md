# Diagnostic decision tree (detail)

## Cannot overfit tiny batch

1. Confirm aug/dropout/WD off; model `.train()`.
2. Print `y` and `y_hat` for the batch; ensure label tensor matches.
3. Check loss expects logits vs probabilities.
4. Try absurd LR briefly (e.g. 1e-1) — if still flat, loop/data bug more likely than “needs tuning”.
5. Grad probe: backward from loss; any param with `grad is None` that should learn?
6. Batch-mixing probe if vectorized ops are complex.

## Loss NaN after k steps

1. Log loss/grad norms each step; find first NaN.
2. Disable AMP; try fp32.
3. Clip grads; lower LR.
4. Check targets for out-of-range class ids.
5. Inspect BN with batch size 1.

## Train improves, val does not

1. Verify val preprocess == train without aug.
2. Leakage / near-dup across splits (`data-judgment` / `cv-data-quality`).
3. Underfit capacity? If train also weak → model/data. If train excellent → regularize / more data (Karpathy §4).
4. Bias–variance style next step (FSDL L7): more capacity vs more data/regularization.

## Optimizer suspicion

1. Print LR each epoch; ensure schedule isn’t zeroing early.
2. Confirm trainable params are in optimizer.
3. Separate LR for pretrained backbone vs head when fine-tuning (`cv-transfer`).
