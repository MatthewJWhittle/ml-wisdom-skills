# AGENTS-FEEDBACK

Repo-local **feedback map**: how an agent observes whether an experiment worked. Created/updated by `feedback-setup`. Other judgment skills reread this file; they do not invent wiring.

> Not an objective-loop contract. Loop runs use `program.md` + `results.tsv` separately.

## Feedback sources

| ID | How to run | What it informs | Primary / guardrail | Notes |
|----|------------|-----------------|---------------------|-------|
| e.g. `val-acc` | `python eval.py --split val` | Overall keep/discard | primary | … |
| e.g. `confusion` | `python scripts/plot_cm.py` | Class confusions | guardrail | … |

## Holdouts & splits

- Train / val / test policy:
- Leakage risks already known:
- Human review hook (if any):

## Trackers

- MLflow / W&B / TensorBoard / none:
- Where runs land (URI, project, local dir):
- How the agent should register a run (command or “defer to tool skill”):

## Recommended gaps

- [ ] Missing feedback the next human/agent should add (plots, finer metrics, slice evals):

## Try log

Durable cross-session record. Append; do not rely on chat history.

| Date | Hypothesis | Change (mutable surface) | Signals | Keep/Discard | Notes |
|------|------------|--------------------------|---------|--------------|-------|
| | | | | | |

## Related

- Objective loop (when used): `program.md`, `results.tsv`
- Judgment pack: compose with HF / Ultralytics / MLflow **tool** skills for commands; this file stores **what this repo observes**.
