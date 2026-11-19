---
name: design-experiment
description: >-
  Plans interpretable ML probes, fair scientific comparisons and performance
  tuning. Use when planning runs, choosing baselines or deciding which
  variables to change or control.
license: MIT
---

# design-experiment

Start with the decision the evidence should support. Read the feedback map for commands, splits, primary signal and guardrails. If a needed signal is missing, use `wire-feedback` to expose it. Resolve data or training correctness failures before tuning.

## Choose the study

| Goal | Design |
|------|--------|
| Diagnose a failure | Change one factor where possible and predict the observable result. Reproduce the failure with the cheapest relevant probe. Use `verify-training` or `diagnose-training` when appropriate. |
| Compare scientific alternatives | Name the **scientific variable** of interest, **nuisance variables** that may need separate tuning for a fair comparison, and **fixed variables** held constant. Give alternatives comparable search budgets and evaluate them by the same protocol. If budget forces a nuisance variable to stay fixed, scope the conclusion to that setting. |
| Optimize performance | Set a search budget, allowed changes, primary signal and guardrails. Explore jointly when useful, record each configuration and confirm promising results under the chosen evaluation protocol. The winning bundle supports a configuration choice. Isolating the effect of a component requires a separate comparison. |

## Pick credible comparators

Pick the smallest baseline set that could change the decision. Explain the chosen controls and any plausible comparator omitted. See [baseline options](references/baseline-ladder.md). Ordinary baseline choices need a rationale and a traceable configuration.

## Run and interpret

Write a short plan in the experiment log or run description. Include the question and expected observations, candidate configurations and allowed changes, controls and budget, split and signals, and the decision the evidence could support. Example:

> CNN vs ViT. Architecture is the scientific variable; learning rate is a nuisance variable. Give each architecture three plausible learning rates and the same training and evaluation budget. Hold data, splits and metric fixed. Compare the selected candidates on untouched data if available. With only one shared learning rate, report the comparison at that rate. For a NaN after a precision change, rerun the same batch and seed with only precision changed and inspect the first nonfinite tensor.

Record the commands, configs and results in `EXPERIMENTS.md`. State what happened, what it supports and what remains uncertain. For evaluation and claims, use `validate-results`. If the target or loss is in question, use `design-learning-objective`. For bounded keep/discard execution, hand the plan to `run-objective-loop`.

Sources are in the [pack references](../../references/sources.md).
