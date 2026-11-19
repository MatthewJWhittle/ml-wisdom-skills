---
name: design-learning-objective
description: >-
  Collaboratively design or review a learning objective when prediction
  intent, label semantics, the head or the loss needs to be worked through.
license: MIT
---

# design-learning-objective

For design, explore the objective with the user. For review, explain what the current objective computes and flag any mismatch with stated intent. For an explicit implementation request with a sufficiently specified design, proceed within scope and ask only about consequential choices that remain open.

1. **Establish intent.** Read relevant task, sampling, label, head and loss code where available. Explain the current behavior in accessible language. Distinguish what is implemented, what the user wants and which alternatives are provisional.
2. **Ask and stop.** For unresolved decisions that could change the objective, ask one or two targeted questions and end the turn. Wait for the actual reply before choosing among those alternatives. For example, “What should a high score mean at an unsurveyed site?” or “When a class label is missing, was the class checked and found absent, or was it left unassessed?” Use each answer to compare viable options and their assumptions. Say when the observations cannot identify the desired target.
3. **Agree the objective in chat.** Walk through the relevant parts of **task → supervision → head → loss → evaluation** with the user. Summarise the agreed design, assumptions and open risks after resolving major choices.

- **Task and supervision.** Establish the prediction unit, population, inference inputs and intended decision. Trace sampling and how labels are generated. Distinguish confirmed negatives from unknown labels and consider observation bias.
- **Head and loss.** Match output shape and semantics to target encoding, masks, weighting and reduction as needed. Explain what an improved prediction rewards. For custom terms, reason through gradient direction, relative scale and edge cases such as an empty mask.
- **Evaluation.** Tie metrics and guardrails to the deployment decision. Identify independent evidence that could challenge the objective, such as survey data for a claim of calibrated presence.

Keep design and review read-only. Agreement alone does not authorise edits to code, documentation, labels or evaluation, or a training run. Use `verify-training` or `build-tiny-loop` for requested executable checks, `design-experiment` to plan comparisons, and `validate-results` to assess evidence. See the [pack sources](../../references/sources.md).
