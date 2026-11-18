---
name: wire-feedback
description: >-
  Inventories how a consumer repo observes experiment success and writes
  AGENTS-FEEDBACK.md as a short router/config (sources, trackers, skill map)
  plus scaffolds EXPERIMENTS.md for the append-only experiment log. Use when
  starting ML/DL work, before objective loops, when feedback wiring is missing
  or stale, or when agents are dumping run notes into AGENTS-FEEDBACK. Trigger
  terms: feedback map, AGENTS-FEEDBACK, EXPERIMENTS.md, experiment log, wiring.
license: MIT
---

# wire-feedback

Creator skill: build **config + experiment log scaffolding** so later judgment has somewhere real to read and write.

- **`AGENTS-FEEDBACK.md`** — short, stable **router/config** (how to observe; where to log; which skill next).
- **`EXPERIMENTS.md`** — append-only **experiment log** (what was tried).

Do **not** train models, run objective loops, or append experiment narratives into `AGENTS-FEEDBACK.md`.

Compose with HF / Ultralytics / MLflow **tool** skills for tracker CLIs; this skill decides **what this repo observes** and **where notes go**.

Templates: [`../../templates/AGENTS-FEEDBACK.md`](../../templates/AGENTS-FEEDBACK.md), [`../../templates/EXPERIMENTS.md`](../../templates/EXPERIMENTS.md).

## Gate: config + log scaffolded

**Pass**:

1. `AGENTS-FEEDBACK.md` at repo root lists runnable feedback sources, holdouts, trackers, gaps, experiment-log path, and skill router.
2. `EXPERIMENTS.md` exists (may be empty aside from the header/template).
3. `AGENTS-FEEDBACK.md` does **not** contain a growing dump of run narratives.

**Waiver**: human explicitly accepts “no map yet” (blocks autonomous loops).

## Steps

### 1. Scan the consumer repo

Find concrete **feedback sources** (commands, scripts, paths — not vibes):

- Eval / test entrypoints and CI jobs that score models
- Metric logs (CSV, JSON, stdout conventions)
- Plot / visualization scripts (**pred overlays**, not numbers-only)
- Holdout / test sets and split definitions
- Existing trackers: MLflow, W&B, TensorBoard, or **none**
- Any existing experiment notes (migrate out of `AGENTS-FEEDBACK.md` if agents already dumped there)
- For CV / dense pred: boundary or surface metrics; **operating-point** PR if detection
- If products are **tiled/stitched**: mosaic/AOI eval or seam-check script
- Optional: short **intended-use rubric** path (5–10 lines humans/agents can apply to a fixed board)

**Done when**: each source has a runnable invocation or an honest “missing.”

### 2. Classify each source

| Field | Question |
|-------|----------|
| How to run | Exact command or “open this path” |
| What it informs | Keep/discard, slice health, leak symptom, … |
| Role | **Primary signal** vs **guardrail signal** |
| Gaps | Too coarse? No plot? No holdout? |

**Done when**: every discovered source has a row; unknowns are gaps, not guesses.

### 3. Write or update `AGENTS-FEEDBACK.md` (config only)

Use the template. Keep it **short**.

Must include:

1. Pointer to **`EXPERIMENTS.md`** (or alternate path if the repo prefers)
2. **Skill router** table (situations → skills)
3. **Feedback sources** table
4. **Holdouts & splits**
5. **Trackers** (register against existing; don’t replace)
6. **Recommended gaps**

If the file already has a fat “Try log” / narrative dump: **move** those entries into `EXPERIMENTS.md` and leave a one-line pointer behind.

**Done when**: a stranger agent can run at least one primary signal from the config alone, and knows where to append experiment notes.

### 4. Scaffold `EXPERIMENTS.md`

Create from template if missing. Do not invent fake historical experiments.

**Done when**: file exists with the entry template visible.

### 5. Recommend missing feedback

Gaps must be specific (“add `scripts/plot_preds.py` for 16-val grid”), not “improve observability.”

For vision, treat as a gap when there is a scalar metric but **no** pred-grid (or mosaic) command — metrics alone invite metric–utility misses ([ADR 0010](../../docs/adr/0010-metric-utility-gap.md)).

### 6. Stop

Point to `design-experiment` / `run-objective-loop` when wiring is ready. Logging of actual tries is done by those skills into **`EXPERIMENTS.md`**.

## Experiment log (other skills write here)

Schema lives in [`../../templates/EXPERIMENTS.md`](../../templates/EXPERIMENTS.md). Chat history is not an experiment log. `results.tsv` remains the dense ledger for one objective-loop run.

## Challenge stance

- Dumping hypotheses/results into `AGENTS-FEEDBACK.md` → reject; move to `EXPERIMENTS.md`.
- “We’ll just look at TensorBoard” without a path/command → reject; write a source row or a gap.
- Replacing a working tracker with a parallel shadow system → reject; register against it.
- Stuffing loop goals into the feedback map → reject; loops use `program.md` + `results.tsv`.

## Related

- [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- [`references/inventory-checklist.md`](references/inventory-checklist.md)

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/)
- Karpathy [autoresearch](https://github.com/karpathy/autoresearch) — separate contract (`program.md`) from history (`results.tsv`)
- [`../../references/sources.md`](../../references/sources.md)
