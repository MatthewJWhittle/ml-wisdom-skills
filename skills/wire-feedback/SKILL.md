---
name: wire-feedback
description: >-
  Inventories how a consumer repo observes experiment success and writes or
  updates AGENTS-FEEDBACK.md (feedback map + Try log). Use when starting ML/DL
  work in a repo, before objective loops, when feedback wiring is missing or
  stale, or when an agent cannot tell keep from discard. Trigger terms:
  feedback map, AGENTS-FEEDBACK, feedback sources, Try log, wiring gaps.
license: MIT
---

# wire-feedback

Creator skill: build the **feedback map** so later judgment and loops have something real to read. Do **not** train models or run objective loops here.

Compose with HF / Ultralytics / MLflow **tool** skills for how to invoke trackers; this skill decides **what this repo observes**.

## Gate: inventory complete → map written

**Pass**: `AGENTS-FEEDBACK.md` exists at the consumer project root, lists runnable feedback sources, holdouts, trackers, gaps, and a Try log section.  
**Waiver**: human explicitly accepts “no map yet” in writing (rare; blocks autonomous loops).

## Steps

### 1. Scan the consumer repo

Find concrete **feedback sources** (commands, scripts, paths — not vibes):

- Eval / test entrypoints and CI jobs that score models
- Metric logs (CSV, JSON, stdout conventions)
- Plot / visualization scripts (curves, confusion, sample grids)
- Holdout / test sets and split definitions
- Existing trackers: MLflow, W&B, TensorBoard, or **none**

**Done when**: you can name each source with a runnable invocation or an honest “missing.”

### 2. Classify each source

For every source, record:

| Field | Question |
|-------|----------|
| How to run | Exact command or “open this path” |
| What it informs | Keep/discard, slice health, leak symptom, … |
| Role | **Primary signal** vs **guardrail signal** |
| Gaps | Too coarse? No plot? No holdout? |

Challenge coarse-only metrics (e.g. single accuracy with no slice, CV with numbers and no image grid).

**Done when**: every discovered source has a row; unknowns are listed as gaps, not guessed.

### 3. Write or update `AGENTS-FEEDBACK.md`

Use the template at [`../../templates/AGENTS-FEEDBACK.md`](../../templates/AGENTS-FEEDBACK.md). Prefer updating in place over rewriting history out of the Try log.

Must include:

1. **Feedback sources** table (runnable)
2. **Holdouts & splits**
3. **Trackers** (integrate; do not replace working MLflow/W&B)
4. **Recommended gaps** (actionable)
5. **Try log** section with the schema below

**Done when**: a stranger agent can run at least one primary signal from the file alone.

### 4. Recommend missing feedback

Add gap bullets when, for example:

- Computer vision / detection without a plot or sample-grid script
- Metrics too coarse to discriminate keep/discard
- No held-out set or leakage policy written down
- Tracker exists but the agent has no register/read command

**Done when**: gaps are specific (“add `scripts/plot_preds.py` for 16-val grid”), not “improve observability.”

### 5. Stop

Do **not** start `program.md` / `results.tsv` objective loops from this skill. Point humans/agents to `design-experiment` + a `run-objective-loop` skill when wiring is ready.

## Try log schema

Append-only rows in `AGENTS-FEEDBACK.md` (durable across sessions):

| Date | Hypothesis | Change (mutable surface) | Signals | Keep/Discard | Notes |
|------|------------|--------------------------|---------|--------------|-------|

Chat history is not a Try log.

## Challenge stance

- “We’ll just look at TensorBoard” without a path/command → reject; write a source row or a gap.
- Stuffing loop goals into the feedback map → reject; loops use `program.md` + `results.tsv`.
- Replacing an existing tracker with a parallel shadow system → reject; register against it.

## Related

- Shared traps: [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- Deeper checklist: [`references/inventory-checklist.md`](references/inventory-checklist.md)

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — visualize and measure before scaling complexity.
- Pack sources index: [`../../references/sources.md`](../../references/sources.md)
