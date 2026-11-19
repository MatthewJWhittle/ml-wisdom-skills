---
name: design-learning-objective
description: >-
  Aligns the prediction target, available supervision, output head and loss.
  Use when framing a new prediction task, choosing a loss for weak or missing
  labels, or checking a proposed head or composite objective before training.
license: MIT
---

# design-learning-objective

Leave a short **task → supervision → head → loss → evaluation** contract with the assumptions and an alternative worth comparing.

1. **Define the prediction.** Name the population, prediction unit, available inputs at inference and decision or quantity the output should represent.
2. **Trace the labels.** Say how examples were observed and labeled, which negatives were actually observed and which labels are unknown. Identify sampling bias, plausible shortcuts and the assumptions connecting labels to the desired target. Where the target is unsupported, narrow the claim or propose independent data that could test it.
3. **Choose a defensible baseline objective.** Specify prediction shape and logits or probabilities, target encoding, masks, sampling or class weights, and reduction. State what a prediction change rewards. For custom or composite terms, reason through direction, relative gradient scales and an edge case such as an empty mask before choosing weights.
4. **Specify evaluation separately.** Name the metric and guardrails tied to the deployment decision and the independent evidence that could disconfirm the formulation. Record expected limits and the cheapest comparison.

Examples:

- One mutually exclusive image label can use softmax cross-entropy. Multiple simultaneously valid labels need a head and loss that support co-occurrence, such as per-class logits with masked BCE when some labels are unknown.
- Recorded sightings and sampled background may support ranking sightings against the sampled background. Unrecorded sites can be unsurveyed. To claim calibrated presence, justify the observation model and seek independent survey evidence.
- For a binary mask, start with a pixel loss and check boundary quality at the deployment threshold. If adding a soft overlap term, check gradients, relative scale and behavior on empty masks. Overlap and usable boundaries can diverge.

Hand the contract to `design-experiment` for comparison, `verify-training` for implementation probes and `validate-results` for the evaluation claim. See the [pack sources](../../references/sources.md).
