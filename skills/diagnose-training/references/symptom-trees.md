# Symptom trees

Walk top → leaf. At each branch, gather the named evidence before continuing.

## A. Cannot overfit tiny batch

Precondition: 1–few batches, aug/dropout/WD off, enough steps.

1. Probe **shapes → labels → loss** first.
2. If loss barely moves: check LR (tree F), frozen stem, wrong trainable params (tree E).
3. If loss decreases on train metric but predictions stay wrong: metric/decode bug,
   not optimization — treat as labels/eval wiring.
4. If still stuck after probes pass: architecture/head mismatch or data pipeline
   feeding constants — escalate with evidence; do **not** widen the model yet.
5. **Gate:** tiny-batch overfit **pass** or **human waiver** before full-set tuning.

## B. Train ↓, val flat

1. Confirm val path uses eval mode, correct split, no train aug.
2. Check leakage vs true generalize fail: near-duplicates can fake “val also ↓”;
   flat val with healthy train often means capacity/regularization/data mismatch —
   but only after correctness probes pass.
3. Slice val: one bad slice vs all flat (see `validate-results`).
4. If train never overfit a tiny batch, this is still tree A — not a capacity problem.

## C. NaN/Inf

1. Binary-search first NaN step (activations vs loss vs grad).
2. Common causes: LR too high (tree F), bad mask → `log(0)`, mixed precision overflow,
   division by zero in custom losses, uninitialized or extreme inputs.
3. Fix the first NaN source; do not lower LR as the only move while Inf inputs remain.
4. **Gate:** several consecutive finite steps **pass** or waiver.

## D. Dead ReLUs

1. Measure fraction of units with output == 0 (or ~0) across a batch; compare early vs late layers.
2. Check init (too negative bias), learning rate, and whether BatchNorm/LayerNorm is
   mis-ordered or bypassed.
3. Dying early after a LR spike → treat as tree F first.
4. Prefer evidence (activation histograms) over swapping activations casually.

## E. Grad flow / param groups

1. Grad norms by block; find the first block with ~0 or Inf.
2. Verify `requires_grad`, hooks, and that the optimizer includes the intended params.
3. Transfer-learning freezes: intentional zero grad ≠ bug; accidental freeze is.
4. Residual / attention paths: ensure skip connections are actually used if grads
   vanish in the branch.

## F. LR too high / too low

| Sign | Suspect |
|------|---------|
| Loss oscillates / diverges / NaNs after a few steps | LR too high (or bad loss scale) |
| Loss crawls; grads healthy but updates tiny | LR too low or scheduler at ~0 |
| Sudden cliff mid-run | schedule milestone / wrong epoch length |

1. Log LR every N steps; confirm schedule math against dataset size.
2. One LR change at a time after probes 1–4 are clean.
3. Challenge: do not grid-search LR while labels or shapes are wrong.

## G. Shape / broadcast / batch-mixing

Classic: a matmul or norm broadcasts across batch so each example “sees” others;
loss can still fall.

1. Assert no unexpected `expand`/`broadcast_to` across dim 0.
2. **Grad-to-input probe:** `loss.backward()`; inspect `x.grad` for example *i*.
   Nontrivial mass on other batch indices ⇒ mixing bug.
3. Fix the op; re-run tiny-batch overfit before celebrating metric recovery.
