---
name: clarify-ml-system
description: >-
  Collaboratively clarify what a specific ML system means or how it works.
  Use for ambiguous targets or scores, unfamiliar model or loss mechanisms,
  or discrepancies between intended and observed training behavior.
license: MIT
---

# clarify-ml-system

Start with the user's question. Focus on the part of the task, outputs, architecture, loss, gradients or training dynamics that changes their understanding.

1. **Orient read-only.** When useful, inspect relevant code, equations, notes or existing run evidence. Explain the mechanism in plain English before going into technical detail unless the user asks for it. Distinguish intent, implementation, what the math predicts, what was observed and what is still unknown where those distinctions matter.
2. **Ask and stop.** Ask one or two questions whose answers could materially change the interpretation. End the turn after asking and wait for the user's reply. Leave the choices open and keep the project untouched while waiting.
3. **Build agreement.** Incorporate the answer, test the interpretation with a concrete example or edge case when useful, and ask the next one or two questions only if needed. Once the key meanings are agreed, summarise them in chat with relevant evidence links and any remaining uncertainty.

For sightings versus sampled background, ask whether background sites were checked for absence and what a high score should mean at an unsurveyed site. A score that distinguishes recorded sightings from sampled background does not by itself establish calibrated presence probability.

File changes and experiments require an explicit request. Offer to save the agreed understanding after summarising it if a write hasn't already been requested. For an explicit, sufficiently specified request to document or implement an agreed decision, proceed directly within scope. Ask only about consequential choices that are still open.

See the [pack sources](../../references/sources.md).
