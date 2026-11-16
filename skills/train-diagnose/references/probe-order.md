# Probe order detail

Evidence checklist for each probe. Prefer repo feedback from `AGENTS-FEEDBACK.md`
when available.

## 1. Shapes

- Print `x.shape`, `y.shape`, model output shape for one batch (train + eval path).
- Look for unintended broadcast: trailing dims of 1 that expand across batch/spatial.
- Confirm collate/padding and channel-last vs channel-first conventions match the model.
- **Fail if:** any silent reshape that merges batch with another axis, or train/eval
  layout disagree.

## 2. Labels

- Histogram / unique counts of labels vs expected classes.
- Spot-check decoded labels against raw inputs (visualize for images/text).
- Verify loss expects indices vs probabilities; `ignore_index` actually ignored.
- **Fail if:** shuffle of `x` without `y`, class-id remap bugs, or all-one-class batches
  that should be mixed.

## 3. Loss

- Finite for several steps; no immediate Inf from log(0) / bad masking.
- At init: roughly matches analytical guess (e.g. `-log(1/C)` for balanced C-way
  softmax). Huge misses → wrong reduction, wrong head, or label bug.
- **Fail if:** NaN/Inf, or init loss off by orders of magnitude without explanation.

## 4. Grads

- After one backward: `grad.norm()` per major module; count NaN/Inf grads.
- Confirm non-zero grad into the stem/backbone when it should train.
- **Fail if:** entire param groups have `grad is None` or ~0 while marked trainable,
  or grads explode before any LR change.

## 5. Optimizer

- List param groups: which modules, LR, WD; biases/BN often should differ.
- Frozen layers: `requires_grad` matches “not in optimizer” intent.
- Scheduler: confirm current LR (log it); silent decay to ~0 is a common false
  “model won’t learn.”
- **Fail if:** empty optimizer, wrong modules frozen, or LR already ~0 at “step 0.”
