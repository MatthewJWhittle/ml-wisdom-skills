---
name: design-experiment
description: >-
  Collaboratively plan diagnostic probes, fair scientific comparisons and
  bounded tuning. Use when choosing comparators, controls, evidence or budget.
license: MIT
---

# design-experiment

Default to a plan in chat. Inspect relevant context read-only, including `AGENTS-FEEDBACK.md` for available commands, splits and signals. Surface data or correctness blockers, missing feedback and resource constraints that affect the study.

When an unresolved choice would change the plan, ask one or two questions and end the turn. Wait for the answer before settling the design. For example, is the goal to isolate the effect of model family or pick the best configuration within a compute budget? Offer credible options and trade-offs without treating a provisional choice as agreed.

| Goal | Design |
|------|--------|
| Diagnose a failure | Change one factor where possible and predict the observable result. For suspected FP16 NaNs, propose replaying the same batch and seed with only precision changed and locating the first nonfinite tensor. |
| Compare scientific alternatives | Name the scientific variable, nuisance variables that may need separate tuning and fixed controls. For CNN vs ViT, use the same evaluation protocol and comparable development tuning budgets with learning-rate ranges appropriate to each. If budget forces one shared rate, limit the conclusion to that setting. |
| Optimize performance | Set a search budget, allowed changes, primary signal and guardrails. Plan joint tuning where useful. Select on development data and reserve the final holdout for evaluating selected candidates. A winning configuration alone cannot isolate a component's effect. |

Choose the smallest credible comparator set and explain what it controls. Use the [baseline options](references/baseline-ladder.md) as a menu. Summarise the agreed question or hypothesis, comparisons and controls, evaluation data, metrics and guardrails, minimum meaningful evidence, budget and limits. Scale the detail to the probe.

Planning leaves code, config, evaluators and logs untouched. Run only on explicit request. If execution is sufficiently specified, proceed within the agreed scope, compute budget and logging choices. Verify the resolved configuration, split and evaluator against the plan first. Any file edits, log writes, keep/discard actions or reverts must be within the authorised scope.

Use `build-tiny-loop` for a requested reusable rapid setup, `run-objective-loop` for explicitly requested bounded keep/discard iteration, and `validate-results` to assess observed evidence. See the [shared failure modes](../../references/things-to-watch.md) and [pack sources](../../references/sources.md).
