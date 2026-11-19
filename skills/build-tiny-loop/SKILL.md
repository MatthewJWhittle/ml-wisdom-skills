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

Build and run a small training entry point from the current project. A request for faster feedback is enough to start.

1. **Trace the real path.** Read the train command and config, dataset/sampler, preprocessing, model, loss, optimizer and step, and existing metrics or plotting hooks. Check the feedback map if present. Identify what already supports fixed subsets, short runs and local compute. Pick the smallest route and say why:
   - **A — Configure.** Use the existing entry point with a fixed subset, short step budget and bounded logging.
   - **B — Extract.** When the trainer couples reusable logic to expensive setup, extract the smallest needed shared callable and have both entry points call it. Preserve the normal path and its defaults.
   - **C — Wrap.** When touching the trainer is impractical, make a thin local driver around the real components. Flag any substituted behavior and limit claims to the path exercised.
2. **Keep the behavior under inspection.** Shrink steps, data or model size only where the change preserves the behavior users need to inspect. Keep a fixed example set for predictions. For dense or imbalanced tasks include examples that exercise empty targets, rare classes or masking when relevant. Record meaningful drift such as dropped classes, smaller batches changing normalization, or a shortened schedule.
3. **Make it rerunnable.** Add the smallest config or command needed. Use local or already authorized compute. Time a smoke run, then rerun the command. Confirm with code inspection and a targeted observation that the expected shared model, loss and train step were reached where relevant.
4. **Expose feedback.** Show loss and relevant components, fixed predictions and targets, and a health signal such as finite gradients, update magnitude or timing. For CV, show predictions over the inputs alongside labels or masks. Reuse project hooks. Inspect the resulting evidence.

Leave the command, typical elapsed time and output paths, plus a short fidelity note naming what is shared, simplified and untested. If a run is blocked, record the blocker and the exact command still to run. A short run can reveal failures and early behavior. Full training, held-out results and throughput at scale need their own evidence.

Use `verify-training` to check correctness, `diagnose-training` for a failure and `design-experiment` when a comparison is needed.
