---
name: validate-results
description: >-
  Chooses metrics and guardrails, checks slices and calibration, and catches
  cases where scores look good but the model fails real use (wrong metric for
  the job, or scoring tiles/crops while shipping a stitched/whole product).
  Use when picking or defending a primary metric, reading eval results, CV
  overlays disagree with Dice/mAP/accuracy, or handing off to cv-error-analysis.
license: MIT
---

# validate-results

Assess what the evaluation evidence supports for the intended use. Report where the conclusion holds, where it is uncertain and what evidence could resolve an important gap.

Use the project’s eval scripts and trackers; this skill does not teach their CLIs.
See [things-to-watch](../../references/things-to-watch.md) and
[ADR 0010](../../docs/adr/0010-metric-utility-gap.md).
For vision failure patterns, also load `cv-error-analysis`.

## Primary vs guardrails

| Role | Job |
|------|-----|
| **Primary** | The main keep/discard score for a loop or release |
| **Guardrails** | Extra checks that can **veto** a primary win |

If the primary improves and a named guardrail fails, report the veto and explain which claim the evidence still supports. For example, an aggregate improvement can coexist with worse recall on a critical slice. Leave promotion, discard or revert actions to the agreed experiment or `run-objective-loop`.

**Gate:** primary + guardrails written down (**pass**), or a **human waiver**
listing what is deferred.

## Choosing metrics

1. Start from the **decision** the model supports (rank, threshold, draw a
   boundary, map a field) — not from a leaderboard default.
2. Under imbalance, prefer PR / cost-aware metrics when accuracy lies.
3. One primary; list guardrails in the open — not “also glance at.”
4. For thresholded deploy: report the **real operating point**. mAP can rise
   while precision/recall at that threshold falls.
5. If you **score parts but ship a whole** (tiles→map, frames→track), add at
   least one check on the whole (or seams), not only per-part scores.

More detail: [metric-and-slices.md](references/metric-and-slices.md).

## Slices

Always read **overall + critical slices** the product cares about (class, site,
sensor, difficulty, …).

- Overall win + critical slice collapse → guardrail fail.
- Tiny slices → ideas for error analysis, not three-decimal claims.

## Calibration & baselines

- If scores are used as confidence, check calibration or ranking — not only
  hard accuracy.
- Record a **human** or strong non-ML baseline when you can; “beats chance”
  alone is weak.
- **Gate:** baseline comparison present (**pass**) or **human waiver**.

## Eval leakage

Hard stop if suspected:

- Near-dups across splits (especially images), fitting preprocessors on all
  data, tuning until test is a second train set.
- Using val for selection is fine; the **final reported number** needs a
  holdout the feedback map names.

**Gate:** clean split story (**pass**) or **human waiver**.

## Evaluation protocol

For a consequential comparison, name the deployment population, prediction and grouping units, data roles and uncertainty that matter for the claim. Use the [short protocol](references/evaluation-protocol.md) to record them. Keep final evaluation separate from selection and state when the evidence is still exploratory.

## Significant digits

- Quote precision the **full eval set** can support. Do not promote noisy
  subset scores to headlines.
- Debug scores are for diagnosis; promotion uses the full eval command from
  `AGENTS-FEEDBACK.md`.

## When to hand off

When the number is trusted but still bad — or the number looks good but
overlays / the whole product fail:

1. Sample failures (and “high score / bad looking” cases for CV).
2. Hand to `cv-error-analysis` (vision) or a domain expert — do not invent a
   new architecture from one confusion-matrix cell.
3. Then data fixes or a scoped `run-objective-loop`.

## Out of scope

Building eval harnesses, Hub metric APIs, or editing the evaluator to raise
the score.
