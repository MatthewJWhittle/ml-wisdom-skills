# AGENTS-FEEDBACK

Repo-local **router + config** for ML judgment: how to observe success, where to log experiments, and which skills to reach for. Created/updated by `wire-feedback`.

Keep this file **short and stable**. Do **not** append experiment narratives here — those go in the experiment log.

## Experiment log

- **Path**: `EXPERIMENTS.md` (repo root unless noted otherwise)
- **Rule**: append-only human-readable experiment entries; never dump run notes into this file

## Skill router

Suggested next skill by situation (judgment pack):

| Situation | Skill |
|-----------|--------|
| “What skills / where do I start?” | `list-skills` |
| Feedback wiring missing/stale | `wire-feedback` |
| New/changed data or labels | `audit-data` (+ `cv-data-quality` for vision) |
| Planning a run / many knobs | `design-experiment` |
| Choosing loss / objective | `design-loss` *(if installed)* |
| Choosing model family | `choose-architecture` *(if installed)* |
| Before long train / new loop | `verify-training` |
| Training looks sick | `diagnose-training` |
| Reading metrics / slices | `validate-results` (+ `cv-error-analysis` for vision) |
| Bounded iterate-keep-discard | `run-objective-loop` |

## Feedback sources

| ID | How to run | What it informs | Primary / guardrail | Notes |
|----|------------|-----------------|---------------------|-------|
| e.g. `val-acc` | `python eval.py --split val` | Overall keep/discard | primary | … |
| e.g. `pred-grid` | `python scripts/plot_preds.py --n 16` | Do overlays look usable? | guardrail | Add if you only have scalars today |
| e.g. `boundary` | `python eval.py --metric hd95` | Are edges sharp enough? | guardrail | When outlines matter more than fill |
| e.g. `mosaic-stitch` | `python scripts/stitch_eval.py` | Do stitched tiles agree? | guardrail | Tiled maps / sliding-window deploy |
| e.g. `confusion` | `python scripts/plot_cm.py` | Which classes confuse? | guardrail | … |

## What “good enough” means (optional)

- Rubric path (plain-language rules):
- Fixed review board (ids / path):

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

## Related artifacts

| File | Role |
|------|------|
| `EXPERIMENTS.md` | Cross-session experiment log (narrative) |
| `program.md` | One objective-loop contract (when looping) |
| `results.tsv` | Dense per-loop ledger (usually untracked) |

Compose with HF / Ultralytics / MLflow **tool** skills for commands; this file stores **what this repo observes** and **where notes go**.
