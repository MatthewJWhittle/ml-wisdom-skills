---
name: design-experiment
description: >-
  Plans interpretable ML probes, fair scientific comparisons and performance
  tuning. Use when planning runs, choosing baselines, deciding which variables to control,
  or preparing an objective loop.
license: MIT
---

# design-experiment

Start with the decision the evidence should support. Read `AGENTS-FEEDBACK.md` for commands, splits and signals. Check that the primary signal and guardrails reflect how the model will be used. Use `wire-feedback` when a needed signal is missing or stale. Pause tuning if `audit-data` or `verify-training` finds an unresolved blocker, or feedback is too coarse to distinguish a useful change.

## Choose the study

| Goal | Design |
|------|--------|
| Diagnose a failure | Change one factor where possible and predict the observable result. Reproduce the failure with the cheapest relevant probe. Use `verify-training` or `diagnose-training` when appropriate. |
| Compare scientific alternatives | Name the **scientific variable** of interest, **nuisance variables** that may need separate tuning for a fair comparison, and **fixed variables** held constant. Give alternatives comparable search budgets and evaluate them by the same protocol. If budget forces a nuisance variable to stay fixed, scope the conclusion to that setting. |
| Optimize performance | Set a search budget, allowed changes, primary signal and guardrails. Explore jointly when useful and record each configuration. Select on development data and reserve the final holdout for evaluating selected candidates. The winning bundle supports a configuration choice. Isolating the effect of a component requires a separate comparison. |

## Pick credible comparators

Pick the smallest baseline set that could change the decision. Explain the chosen controls and any plausible comparator omitted. See [baseline options](references/baseline-ladder.md). Ordinary baseline choices need a rationale and a traceable configuration.

## Plan and verify the change

Record the question and expected observation, comparator, split and signals, and time or compute budget. Name the files and settings allowed to change. For a keep/discard decision, agree on the primary comparison or threshold and the guardrails before running.

- **Scientific comparison.** Compare a CNN and ViT with the same data and metric. Give each a comparable budget to tune learning rate on development data, with ranges appropriate to each. With a single shared learning rate, limit the claim to that setting.
- **Diagnostic probe.** For a NaN after a precision change, rerun the same batch and seed with only precision changed. Inspect the first nonfinite tensor.

Before running, compare the actual diff and resolved configuration with the plan. Verify the split, preprocessing and evaluator. If debugging a new path, grow the smallest working setup in steps you can inspect and verify.

## Run, record and decide

Append the command, code and config identifiers, measured signals and interpretation to the [experiment log](../../templates/EXPERIMENTS.md). Use the log path in the feedback map when available. State what the result supports and what remains uncertain.

- **Keep** a trial change when the agreed primary comparison and guardrails support it. Record why and retain the matching configuration.
- **Discard** a trial change when it misses the agreed rule or a guardrail fails. Revert the trial change and record what you learned.
- **Inform** when a probe answers a question without a promotion decision or when evidence is inconclusive. Record the next useful check.

Use `validate-results` for evaluation and claims, `design-learning-objective` for target or loss decisions, and `run-objective-loop` for bounded keep/discard execution.

See the [shared failure modes](../../references/things-to-watch.md) and [pack sources](../../references/sources.md).
