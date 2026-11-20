---
name: verify-training
description: >-
  Checks training correctness before scaling (pre-forward tensors and labels,
  loss and head at init, tiny-batch fit, input-independent controls). Use before
  long trains, after pipeline changes, or when loss/metrics look wrong. Trigger
  terms: loss at init, overfit tiny batch, verify-training, correctness gate,
  waiver.
license: MIT
---

# verify-training

Check whether training behaves as intended and report what the evidence covers. Use cheap probes before a long run. Start from the user's question: an isolated fit can test whether an objective learns, while readiness to scale calls for a short check through the intended training path. Adapt the probes to the model and task.

Compose with HF Trainer / Ultralytics / Lightning tool skills for the actual train commands. Use the project’s real training path for the checks below.

Reread `AGENTS-FEEDBACK.md` if present; use its eval commands when a gate needs a metric.

## Master gate

Fix clear correctness bugs with an unambiguous, scoped repair and rerun affected checks. If the correction depends on a design choice, explain the alternatives and wait for the user. When a bug exposes a coverage gap, propose a small check that would catch it. Ask if adding a lasting check involves a material trade-off about what the user wants covered.

For a readiness assessment, report which configuration was tested, what passed and what still blocks scaling. An applicable check that leaves a material correctness risk unresolved blocks scaling until a follow-up probe resolves it or a human explicitly waives that risk. Record the check and the accepted risk with the waiver. For an inapplicable check, record why and use an alternative if it addresses the same risk.

When reporting a critical check in `EXPERIMENTS.md`, record the command or probe, batch or split, expected and observed results, and the artifact path when available. Use **PASS** for an observed match, **FAIL** for a demonstrated mismatch, **INCONCLUSIVE** for ambiguous evidence, **NOT_RUN** when an applicable check was skipped or blocked, and **N/A** when it does not apply. Keep a waiver alongside the observed status.

## Correctness checklist

Start with the controlled setup and inspect the actual batch before optimization. After a fix, rerun the affected check and any downstream checks it could change.

### 1. Fixed seed (diagnostic)

Set seeds for Python / NumPy / the framework and relevant data workers. Record device and precision when they affect the comparison. Fixed seeds help repeatability.

| | |
|--|--|
| **Probe** | When repeatability matters, rerun the same one-step or tiny train from the same batch and initial model/optimizer state. Compare loss or outputs within a tolerance suited to the run. |
| **Investigate** | A large unexplained difference. Check remaining randomness, data order, and nondeterministic operations on the hardware path. |

### 2. Disable augmentation (diagnostic setup)

Turn off random augmentation, including mixup and auto-augment, for the first isolated fit. Hold other stochastic operations fixed where that preserves the training behavior under investigation.

| | |
|--|--|
| **Check** | Confirm the actual training path uses the intended controlled batch. |
| **Alternative** | If augmentation is integral to the objective, fix its draws or cache the transformed example and target. Record which source of variation the probe still includes. |

### 3. Visualize tensors immediately before forward (invariant)

Dump or plot the exact tensors and targets entering the model. For vision, decode the transformed inputs alongside their labels or masks.

| | |
|--|--|
| **Pass** | Shapes, layout, dtypes, ranges and masks match the contract. Targets align with inputs, and values are finite where required. |
| **Fail** | Wrong layout, mismatched labels or invalid values. Fix the pipeline and inspect the batch again. |

### 4. Loss at init (task-specific)

Measure the loss before the first update. Compare it with an independently calculated expectation from the intended objective, including the actual logits and targets, weights, masks, and reduction. Use a hand-checkable batch or component for a custom loss. See [initial-loss notes](references/init-loss-notes.md).

| | |
|--|--|
| **Pass** | The observed loss agrees with the task-specific expectation. Equal logits give `ln(C)` for ordinary unweighted mean softmax cross-entropy with normalized targets. |
| **Investigate** | Unexpected scale or an unexplained mismatch. Check reduction, weights, label encoding, masking and the head before assigning a cause. |

### 5. Final-layer init (task-specific)

Check final-layer scale and bias against the loss and output contract, including whether the head should reflect a class prior, target scale, or pretrained output distribution.

| | |
|--|--|
| **Check** | Inspect initial logits or predictions for the expected scale, bias and distribution. |
| **Investigate** | Unintended saturation or extreme outputs. Check head initialization and whether the run loaded the intended checkpoint. |

### 6. Input-independent baseline (task-specific)

Fit a constant or prior predictor using training labels and score it on the same held-out split and metric as the model. If you suspect leakage, use a control that breaks the input–target association. Split by the appropriate groups first, then apply the same control construction independently within each split, such as fixed random inputs drawn separately for train and holdout.

| | |
|--|--|
| **Interpret** | A high-capacity model can fit fixed noise on its training examples. High training accuracy with held-out scores at the no-signal baseline is consistent with memorization. |
| **Investigate** | Held-out performance meaningfully above the no-signal baseline, accounting for sampling variation. Check split overlap and surviving signals before concluding there is leakage. |
| **Alternative** | For next-token prediction, a suitable token-frequency baseline or a targeted split/overlap audit can be more informative than training on noise. |

### 7. Overfit a tiny batch (diagnostic)

Repeat one or two fixed batches on the real train step. Set an attainable loss or task-specific fit target before interpreting the result. For example, smoothed labels have a positive cross-entropy floor even at the optimum. Regularization and conflicting targets can also limit fit.

| | |
|--|--|
| **Check** | Compare the loss and fixed predictions with the expected fit. Look for the model to learn the examples within a reasonable step budget. |
| **Investigate** | If fit stalls below expectations, check inputs, labels and learning rate, then inspect gradients and parameter updates. Use `diagnose-training` for ordered probes. Consider a small numerical gradient check when a custom differentiable operation is suspect. |

### 8. Short real training run (diagnostic)

After the isolated checks, restore the intended augmentation and training settings. Run a short, representative train and inspect loss components, fixed predictions and updates against the expected behavior.

| | |
|--|--|
| **Check** | Look for the expected learning trend or another relevant health signal given the schedule. |
| **Investigate** | Treat a brief flat or noisy trace as inconclusive when the schedule or objective can explain it. Probe unexpected nonfinite values or ineffective updates before scaling. |

## Before scaling

Use the short run to decide whether to make a controlled increase in complexity (`design-experiment`). Resolve applicable blocking checks or record the explicit human waiver before a long run or objective loop. Evaluate generalization separately.

## Challenge stance

- “It’ll work at full scale” after an unexplained tiny-batch failure → probe the blocker before scaling.
- An unexpectedly high initial loss → check the objective and head contract on a known batch.
- “Shapes match” → inspect actual values and target alignment too.

## Related

- Traps: [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- Init expectations: [`references/init-loss-notes.md`](references/init-loss-notes.md)
- Data first: `audit-data` · process: `design-experiment`

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — verify loss@init; overfit tiny batch; visualize before the model.
- Pack sources: [`../../references/sources.md`](../../references/sources.md)
