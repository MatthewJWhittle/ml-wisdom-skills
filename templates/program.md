# program.md — objective loop contract

Fill before starting a `run-objective-loop`. One objective per file/run.

## Goal

- **Objective** (one sentence):
- **Primary signal** (name, direction better↑/↓, how to read):
- **Guardrail signals** (fail if…):

## Scope — mutable surface

- **Allowed to change**: ( ) hyperparameters only ( ) one training file: `____` ( ) training recipe ( ) feature/pipeline code ( ) other: ____
- **Forbidden**: raw datasets, untracked data dirs, secrets, evaluator/metric definitions (unless user explicitly expands scope), unrelated packages
- **Git**: work on branch `____` (create if needed). Ask before force-push, reset of shared branches, or deleting files outside the mutable surface.

## Budget

- **Per-experiment budget**: (wall clock / max steps / max epochs)
- **Kill / treat as crash if exceeds**:
- **Stop when**: (human interrupt / N experiments / primary plateau rule)

## Keep / discard

- **Keep if**: primary improves **and** no guardrail fails
- **Discard if**: primary equal/worse, guardrail fail, or crash (after quick fix attempts for typos only)
- **Evaluator is immutable** during the loop

## Logging

- Append every attempt to `results.tsv` (see template header)
- Mirror to project tracker if `AGENTS-FEEDBACK.md` defines one
- On loop end: append a condensed entry to `EXPERIMENTS.md`
