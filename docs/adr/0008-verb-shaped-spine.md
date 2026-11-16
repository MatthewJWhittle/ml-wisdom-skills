# Verb-shaped spine for workflow invocation

Nobody was depending on the first public cut yet, so we overhauled spine skill IDs from capability nouns (`data-judgment`, `train-verify`, …) to **verbs** that match how skills get called in a session (`audit-data`, `verify-training`, …).

Rationale: the highest-use skills elsewhere (`grill-me`, `code-review`, `research`, `prototype`) are speech acts. Descriptions still carry discovery triggers; names carry the slash/self-call UX. CV overlays remain `cv-*`.

| Old | New |
|-----|-----|
| `feedback-setup` | `wire-feedback` |
| `data-judgment` | `audit-data` |
| `experiment-discipline` | `design-experiment` |
| `train-verify` | `verify-training` |
| `train-diagnose` | `diagnose-training` |
| `evaluate` | `validate-results` |
| `objective-loop` | `run-objective-loop` |
