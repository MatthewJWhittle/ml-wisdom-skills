---
name: verify-training
description: >-
  Runs correctness gates before scaling deep learning training (fixed seed,
  batch inspection, loss at init, tiny-batch fit and input-independent
  controls). Use before
  long trains, after pipeline changes, or when loss/metrics look wrong. Trigger
  terms: loss at init, overfit tiny batch, verify-training, correctness gate,
  waiver.
license: MIT
---

# verify-training

Prove the training stack can learn **anything** before you burn GPU hours. Default context: neural nets; adapt classical ML to “model fits toy data / loss moves the right way.”

Compose with HF Trainer / Ultralytics / Lightning tool skills for the actual train commands. This skill owns **gates**, not CLI flags.

Reread `AGENTS-FEEDBACK.md` if present; use its eval commands when a gate needs a metric.

## Run the checks

Start with the cheapest run that exercises the real training path. Use a fixed batch and disable stochastic augmentation where practical for the initial probes. Seed relevant generators and record device, precision and settings when they affect the comparison. Fixed seeds help repeatability; compare repeated runs within a useful tolerance when determinism matters.

1. **Batch and target contract (invariant).** Inspect the actual inputs and targets immediately before forward. Check shapes, ranges, dtypes, masks and finiteness where required. For vision, show transformed inputs with their labels or masks to catch alignment and encoding errors.
2. **Initial predictions and loss (task-specific).** Inspect head outputs for saturation or unexpected scales. Calculate the expected loss for the actual logits, targets, weights, masking and reduction. See [initial-loss examples](references/init-loss-notes.md).
3. **Fit a tiny batch (diagnostic).** Repeat one or two fixed batches. Look for the fit the model and objective can attain. Soft targets, regularization and conflicting labels can impose a loss floor. If progress is unclear, inspect gradients and parameter updates or run a simpler probe that preserves the mechanism in question.
4. **Input-independent comparison (task-specific).** Use a train-only prior or constant predictor as a reference where meaningful. If testing a suspected shortcut, break the input–target association without leaking across groups, preserve a clean holdout and check what other predictive signals remain. Training-set memorization of fixed noise is possible. Unexpected held-out skill warrants investigating leakage, split overlap and surviving signals.
5. **Short real train (diagnostic).** Once the small checks are sound, inspect loss components, predictions and updates on a short representative run. Expect behavior appropriate to the objective and schedule. A noisy or flat trace calls for a targeted probe before scaling.

## Record evidence and decide

For consequential checks, record the command or observation path, the measured result and the expected behavior in the experiment log or PR. Use **PASS** for an observed match, **FAIL** for a demonstrated mismatch, **INCONCLUSIVE** for ambiguous evidence or **NOT_RUN** when skipped or blocked. Give the reason and the next useful check for unresolved results.

Fix demonstrated correctness failures before scaling. For a task-specific check that does not apply, record why and use an appropriate alternative if it addresses the same risk. When a material risk remains unresolved, state what evidence or explicit waiver would support scaling. Passing the tiny run supports increasing complexity cautiously. Generalization needs separate evaluation.

## Related

- Traps: [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- Init expectations: [`references/init-loss-notes.md`](references/init-loss-notes.md)
- Data first: `audit-data` · process: `design-experiment`

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — verify loss@init; overfit tiny batch; visualize before the model.
- Pack sources: [`../../references/sources.md`](../../references/sources.md)
