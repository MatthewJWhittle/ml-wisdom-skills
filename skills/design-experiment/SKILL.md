---
name: design-experiment
description: >-
  Collaboratively plan diagnostic probes, fair scientific comparisons and
  bounded tuning. Use when choosing comparators, controls, evidence or budget.
license: MIT
---

# design-experiment

Agree what an experiment should establish and record a plan that someone else can execute and interpret. Inspect relevant context read-only, including `AGENTS-FEEDBACK.md` for available commands, splits and signals. Surface data or correctness blockers, missing feedback and resource constraints that affect the study.

When an unresolved choice would change the plan, ask one or two questions and end the turn. Wait for the answer before settling the design. For example, is the goal to isolate the effect of model family or pick the best configuration within a compute budget? Offer credible options and trade-offs without treating a provisional choice as agreed.

| Goal | Design |
|------|--------|
| Diagnose a failure | Change one factor where possible and predict the observable result. For suspected FP16 NaNs, propose replaying the same batch and seed with only precision changed and locating the first nonfinite tensor. |
| Compare scientific alternatives | Name the scientific variable, nuisance variables that may need separate tuning and fixed controls. For CNN vs ViT, use the same evaluation protocol and comparable development tuning budgets with learning-rate ranges appropriate to each. If budget forces one shared rate, limit the conclusion to that setting. |
| Optimize performance | Set a search budget, allowed changes, primary signal and guardrails. Plan joint tuning where useful. Select on development data and reserve the final holdout for evaluating selected candidates. A winning configuration alone cannot isolate a component's effect. |

## Plan and verify the change

Choose the smallest credible comparator set and explain what it controls. Use the [baseline options](references/baseline-ladder.md) as a menu. Confirm the plan with the user, then record it in the repo for handoff. For a read-only request, deliver the plan in chat. Scale its detail to the probe and cover what matters for execution:

- **Question and comparisons.** State the objective or hypothesis, expected observation, comparators, controls and limits.
- **Run.** Identify the entry points, configs, files and settings allowed to change, budget and sequence. For a series, say which findings determine the next run.
- **Feedback.** Identify evaluation data and commands, primary signal, guardrails and where to read outputs. Choose signals that reflect the decision the model supports.
- **Decision.** Agree what evidence would support a change, rule it out or leave the result inconclusive.

## Run, record and decide when explicitly requested

For a planning request, stop after delivering the plan. For a sufficiently specified execution request, proceed within the agreed scope and budget. Before running, compare the actual diff and resolved configuration with the plan. Verify the split, preprocessing and evaluator. If debugging a new path, grow the smallest working setup in steps you can inspect and verify.

After a meaningful run, record the command, code and config identifiers, measured signals and interpretation in the experiment log, following the agreed logging scope. Use the path in the feedback map when available. State what the result supports and what remains uncertain.

- **Keep** when the agreed primary comparison and guardrails support the trial. Explain why and retain the matching configuration if in scope.
- **Discard** when the trial misses the agreed rule or a guardrail fails. Record what you learned. Revert the trial change only when authorised.
- **Inform** when the probe answers a question without a promotion decision or when the evidence is inconclusive. Record the next useful check.

Apply keep/discard actions only within the authorised execution scope. Otherwise report the recommendation.

Use `build-tiny-loop` for a requested reusable rapid setup, `run-objective-loop` for explicitly requested bounded keep/discard iteration, and `validate-results` to assess observed evidence. See the [shared failure modes](../../references/things-to-watch.md) and [pack sources](../../references/sources.md).
