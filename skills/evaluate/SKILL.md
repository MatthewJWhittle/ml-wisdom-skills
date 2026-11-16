---
name: evaluate
description: >-
  Chooses metrics, slices, and calibration checks; guards against eval leakage,
  Goodhart gaming, and over-precise reporting. Use when picking or defending a
  primary metric, reading full-set vs subset scores, comparing to a human
  baseline, auditing train/eval contamination, or handing off failures to
  error analysis (including cv-error-analysis for vision).
license: MIT
---

# Evaluate

Judgment for **what “better” means** and whether the number is trustworthy.
Compose with project eval scripts and trackers; this skill does not document them.

Cross-check [things-to-watch](../../references/things-to-watch.md) (Evaluation &
Goodhart). For vision failure taxonomy, also load `cv-error-analysis`.

## Primary vs guardrails

| Role | Job |
|------|-----|
| **Primary signal** | The keep/discard metric for a loop or release candidate |
| **Guardrail signals** | Can **veto** a primary win (slice collapse, calibration break, VRAM blow-up, leakage symptoms) |

**Goodhart rule:** a primary improvement that trips a critical guardrail is **not**
a keep. Record the veto. See `objective-loop` keep rules and
[program.md](../../templates/program.md).

**Gate:** primary + agreed guardrails defined (**pass**) or **human waiver**
naming which guardrails are deferred.

## Metric choice

1. Match the **decision** the model supports (ranking vs thresholding vs
   calibration-sensitive deploy).
2. Prefer metrics that stay informative under class imbalance (PR, cost-weighted
   error) when accuracy lies.
3. One primary; list guardrails explicitly — do not bury them as “also look at.”
4. Thresholds: report the operating point; do not chase a threshold on the same
   set used for final claim.

Detail: [metric-and-slices.md](references/metric-and-slices.md).

## Slices

Always read **global + critical slices** (class, source, difficulty, device,
demographic or operational strata the product cares about).

- A global win with a collapsed critical slice → guardrail fail.
- Tiny slices: treat as hypotheses for error analysis, not three-decimal claims.

## Calibration & baselines

- When probabilities drive decisions: check calibration (reliability / ECE-style
  glance) or ranking quality — not only hard accuracy.
- Record a **human baseline** or strong non-ML baseline when available; “beats
  chance” alone is a weak gate.
- **Gate:** baseline comparison present (**pass**) or **human waiver**.

## Leakage of eval into train

Treat as a hard stop if suspected:

- Near-duplicate overlap (especially images), target encoding from full data,
  aug pipelines that differ only on paper, tuning on the final test set until it
  is a second train set.
- Selection on val is expected; **final reported number** needs a held-out or
  nested protocol the feedback map names.

**Gate:** split/contamination story coherent (**pass**) or **human waiver**.

## Significant digits & full eval

- Quote metrics at a precision the **full eval set** and variance can support.
  Do not promote leaderboard noise from a subset to three decimals.
- Subset/debug scores are for diagnosis; promotion requires the full agreed eval
  command from `AGENTS-FEEDBACK.md`.

## Error analysis handoff

When the number is trusted but still bad:

1. Sample failures (and confident wrongs); group by cause hypothesis.
2. Hand off to modality overlays (`cv-error-analysis` for images/frames) or
   domain experts — do not invent architecture changes from a single confusion
   matrix cell.
3. Feed groups back into data/label fixes or a scoped `objective-loop`.

## Out of scope

Implementing eval harnesses, Hub metric APIs, or silently editing the evaluator
to improve a score (forbidden in objective loops).
