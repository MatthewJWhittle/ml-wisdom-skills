---
name: train-verify
description: >-
  Runs correctness gates before scaling deep learning training (fixed seed,
  aug off, loss at init, final-layer init, input-independent baseline, overfit
  tiny batch, visualize tensors pre-forward, decreasing train loss). Use before
  long trains, after pipeline changes, or when loss/metrics look wrong. Trigger
  terms: loss at init, overfit tiny batch, train-verify, correctness gate,
  waiver.
license: MIT
---

# train-verify

Prove the training stack can learn **anything** before you burn GPU hours. Default context: neural nets; adapt classical ML to “model fits toy data / loss moves the right way.”

Compose with HF Trainer / Ultralytics / Lightning tool skills for the actual train commands. This skill owns **gates**, not CLI flags.

Reread `AGENTS-FEEDBACK.md` if present; use its eval commands when a gate needs a metric.

## Master gate

**Pass**: every checklist gate below is PASS, or each failure has an explicit human **waiver** naming the gate.  
**Fail**: any gate FAIL without waiver → do not scale batch, model, aug, or multi-GPU.

Record results in the Try log or a short `train-verify` note in the PR.

## Correctness checklist

Run in order. Fix and re-run from the first failure unless waived.

### 1. Fixed seed

Set seeds for Python / NumPy / framework; note cudnn deterministic tradeoffs if any.

| | |
|--|--|
| **Pass** | Rerun same one-step (or tiny) train → bit-similar or acceptably close loss |
| **Fail** | Wild jump with no code change |
| **Waiver** | Human accepts nondeterminism for this hardware path |

### 2. Disable augmentation

Turn off random aug, mixup, auto-augment, stochastic dropout schedules used as “aug.”

| | |
|--|--|
| **Pass** | Train path confirmed aug-free for gates 3–7 |
| **Fail** | Random geom/color still on |
| **Waiver** | None for first correctness pass — re-enable only after tiny-batch overfit passes |

### 3. Loss at init

Before training, compute loss on a batch (or short eval).

| | |
|--|--|
| **Pass** | Loss ≈ theoretical init (e.g. `-log(1/C)` for balanced softmax CE); within a sensible factor |
| **Fail** | Orders of magnitude off → reduction, weights, label encoding, or head bug |
| **Waiver** | Human documents expected init loss for a custom objective |

### 4. Final-layer init

Check last layer scale/bias for the loss (e.g. classification bias to prior; avoid huge initial logits).

| | |
|--|--|
| **Pass** | Init matches recipe for this loss; gate 3 still holds |
| **Fail** | Saturated softmax / exploding initial predictions |
| **Waiver** | Framework default explicitly accepted |

### 5. Input-independent baseline

For supervised discriminative tasks: train briefly on **same labels** with inputs replaced by noise / zeros (or shuffled inputs).

| | |
|--|--|
| **Pass** | Stays near chance — model does **not** learn well on nonsense inputs |
| **Fail** | Strong performance on noise/shuffle → leakage, collapsed inputs, or label bug |
| **Waiver** | Skip with note for pure generative LM next-token-on-noise; required for discriminative tasks |

### 6. Overfit a tiny batch

Take ~1–2 batches, train until train loss → ~0 (or perfect train metric). No aug. Fixed seed.

| | |
|--|--|
| **Pass** | Loss approaches ~0 / perfect fit in reasonable steps |
| **Fail** | Cannot overfit → data pipe, labels, LR, grad kill, wrong tensors |
| **Waiver** | Human accepts irreducible loss (e.g. noisy labels) with a stated floor |

### 7. Visualize tensors immediately before forward

Dump or plot the actual batch tensor/ Prem immediately pre-model (and labels).

| | |
|--|--|
| **Pass** | Values/shapes look right; labels align with inputs; no silent NaN/inf |
| **Fail** | Garbage, misaligned labels, wrong layout |
| **Waiver** | None — fix the pipe |

### 8. Decreasing train loss

Short real train (aug still off or minimal): train loss trend down across steps.

| | |
|--|--|
| **Pass** | Clear downward trend |
| **Fail** | Flat/NaN/up — stop scaling |
| **Waiver** | Documented non-monotonic objective with other health signals |

## After all gates pass

Re-enable aug and scale **one** complexity click (`experiment-discipline`). Do not open a long objective loop on a failing verify.

## Challenge stance

- “It’ll work at full scale” after tiny-batch fail → blocked.
- Softmax CE init at 10+ without explanation → fail gate 3.
- Skipping visualize because “shapes match” → fail gate 7.

## Related

- Traps: [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- Init expectations: [`references/init-loss-notes.md`](references/init-loss-notes.md)
- Data first: `data-judgment` · process: `experiment-discipline`

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — verify loss@init; overfit tiny batch; visualize before the model.
- Pack sources: [`../../references/sources.md`](../../references/sources.md)
