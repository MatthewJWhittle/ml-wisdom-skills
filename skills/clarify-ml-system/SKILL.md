---
name: clarify-ml-system
description: >-
  Collaboratively clarify what a specific ML system means or how it works.
  Use for ambiguous targets or scores, unfamiliar model or loss mechanisms,
  or discrepancies between intended and observed training behavior.
license: MIT
---

# clarify-ml-system

Build shared clarity about what the system does. Start with the user's question. Focus on the part of the task, outputs, architecture, loss, gradients or training dynamics that changes their understanding.

1. **Orient read-only.** When useful, inspect relevant code, equations, notes or existing run evidence. Explain the mechanism in plain English before going into technical detail unless the user asks for it. Distinguish intent, implementation, what the math predicts, what was observed and what is still unknown where those distinctions matter.
2. **Ask and stop.** Ask one or two questions whose answers could materially change the interpretation. End the turn after asking and wait for the user's reply. Leave the choices open and keep the project untouched while waiting.
3. **Build agreement.** Incorporate the answer, test the interpretation with a concrete example or edge case when useful, and ask the next one or two questions only if needed. Summarise the understanding, evidence and remaining uncertainty for the user. Once they agree, update the relevant glossary entry or system documentation in the repo. If neither exists, create a short note in the appropriate location. If the request or workspace rules prevent a write, keep the agreed explanation in chat.

If the discussion exposes a design decision, capture the open question, evidence and why it matters so it can be taken into a separate grilling session or ticket. Code changes, experiments and opening tickets require an explicit request. Proceed directly on a sufficiently specified request.

See the [pack sources](../../references/sources.md).
