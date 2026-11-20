---
name: build-tiny-loop
description: >-
  Builds a fast, observable training playground using the project's real ML
  pipeline. Use when training feedback is too slow, when setting up a tiny
  run for development, or when a model, loss or augmentation change needs
  a cheap rerunnable check.
license: MIT
---

# build-tiny-loop

Deliver and demonstrate a cheap, rerunnable path through the real training pipeline for testing model, loss or pipeline changes. Start with a read-only assessment of what the loop must preserve.

1. **Trace the real path.** Read the train command and config, dataset/sampler, preprocessing, model, loss, optimizer and step, and existing metrics or plotting hooks. Check the feedback map if present. Identify what already supports fixed subsets, short runs and local compute. Propose the smallest route and say why:
   - **A — Configure.** Use the existing entry point with a fixed subset, short step budget and bounded logging.
   - **B — Extract.** When the trainer couples reusable logic to expensive setup, extract the smallest needed shared callable and have both entry points call it. Preserve the normal path and its defaults.
   - **C — Wrap.** When touching the trainer is impractical, make a thin local driver around the real components. Flag any substituted behavior and limit claims to the path exercised.
2. **Resolve trade-offs before building.** If a shortcut changes what the loop can test, explain the impact, ask the user and wait. For example, caching encoder features can speed up decoder comparisons with a fixed encoder, but it removes the gradient path needed to test end-to-end training. Proceed when the relevant choices are settled.
3. **Keep the behavior under inspection.** Set a practical local step or time budget. Reduce steps and data first. Shrink the model only if needed and if the smaller version preserves the behavior under inspection. Keep a fixed example set for predictions. For dense or imbalanced tasks include examples that exercise empty targets, rare classes or masking when relevant. Record meaningful drift such as dropped classes, smaller batches changing normalization, or a shortened schedule.
4. **Make it rerunnable.** Add the smallest config or command needed. Use local or already authorized compute. Time a smoke run, then make a reversible config change such as lowering the step cap and rerun. Check that it took effect. Confirm with code inspection and a targeted observation that the expected shared model, loss and train step were reached where relevant.
5. **Expose feedback.** Show loss and relevant components, fixed predictions and targets, and a training-health signal such as gradient finiteness or parameter updates. For CV, show predictions over the inputs alongside labels or masks. Reuse project hooks. Inspect the resulting evidence.

Record the rerun command, elapsed time, observations and output paths in the project’s experiment log. Add a short fidelity note naming what is shared, simplified and untested. If a run is blocked, record the blocker and the exact command still to run. A short run can reveal failures and early behavior. Full training, held-out results and throughput at scale need their own evidence.

Reuse the tiny entry point and its evidence for `verify-training` checks that it can exercise. Use `diagnose-training` for a failure and `design-experiment` when a comparison is needed. See the [pack sources](../../references/sources.md).
