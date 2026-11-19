---
name: clarify-ml-system
description: >-
  Builds a shared explanation of what an ML task, model, loss or training
  mechanism actually does. Use when terminology is ambiguous, an unfamiliar
  mechanism needs explaining, or intended behavior conflicts with equations,
  code or observed predictions.
license: MIT
---

# clarify-ml-system

Start with the claim or uncertainty the user wants to resolve. Ask one or two questions that would change the explanation, and let the answers guide which code, equations or observations to inspect. State ambiguous terms in concrete language and invite the user to check that meaning.

1. **Describe one example end to end.** Use only the views needed: task and supervision, model transformations, objective and gradients, or training dynamics. Begin with the user-facing meaning. Add equations, tensor shapes or update steps where they resolve ambiguity.
2. **Challenge the explanation.** Work through a small example and a counterexample: missing label, constant predictions, reversed ordering or a batch with no positives. Ask what the user expects when the prediction changes. Derive what the equation implies, trace what the implementation computes, and compare with observed behavior if a cheap check is available.
3. **Reconcile differences.** Distinguish intent, mathematical expectations, implementation, and observations. Cite the relevant path, equation or run when available. Mark open assumptions and identify the smallest useful check when the evidence is missing or disagrees.

For example, a model trained on recorded sightings versus sampled background may score the contrast between those groups. Ask how observation effort and background sampling work before interpreting its output as a probability of presence. Trace target construction and loss, then check what happens to a location with no survey.

Close with a short conceptual model the user can correct: agreed terms, inputs and outputs, mechanism, evidence and unresolved assumptions. If the user wants it retained, update their existing project note or create one short note for this question. Carry the agreed target and assumptions into supervision and loss design. Use `diagnose-training` when the next step is debugging a run. See the [pack sources](../../references/sources.md).
