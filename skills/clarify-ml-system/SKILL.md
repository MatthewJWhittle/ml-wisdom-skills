---
name: clarify-ml-system
description: >-
  Builds and maintains a shared glossary for an ML system. Use when terms
  such as target, negative or probability are ambiguous, an unfamiliar
  mechanism needs explaining, or intended behavior conflicts with the math,
  code or observed predictions.
license: MIT
---

# clarify-ml-system

Build a shared vocabulary the project can use in discussions, docs and reviews. Start with the user's current question and focus on the terms that change how people understand the system.

1. **Find the glossary.** Reuse the project's existing glossary and follow any documented pointer for this model or context. If the project has no glossary, create `GLOSSARY.md` in the project root once the first definition is agreed.
2. **Resolve an ambiguous term.** Compare how the user, glossary and code use it. If one word covers two concepts, propose a name for each. Ask one or two questions at a time and work through a concrete example and a boundary case. Use the model's inputs, targets, outputs or training behavior that matter to the question.
3. **Ground the definition.** Inspect the relevant code, equations or run outputs where available. Distinguish what the user intends, what the math implies, what the code does and what has been observed. Surface discrepancies for the user to resolve. Keep unconfirmed meanings open and name the smallest check that would settle them.
4. **Update the glossary as terms are agreed.** Write a short definition under each preferred term. Include a scope, contrast or example only when it disambiguates the term. Revise the existing entry when the meaning changes. Keep implementation details, experiment results and unresolved questions in the discussion or the project's existing notes.

For a model trained on recorded sightings and sampled background, ask how it labels a site that nobody surveyed. If the data pipeline confirms that background sites were sampled without verifying absence, two glossary entries might be:

- **Recorded presence** — a site where the species was observed.
- **Background site** — a site sampled for comparison without confirming whether the species is present or absent.

Use those terms to check what the model's score represents before anyone calls it a probability of presence.

At the end, show the glossary entries added or revised and any open terms. If there is no writable project, give the user glossary-ready entries in chat. Carry the agreed definitions into supervision and loss design, or use `diagnose-training` when the next step is debugging a run. See the [pack sources](../../references/sources.md).
