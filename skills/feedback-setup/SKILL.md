---
name: feedback-setup
description: Inventories how a repo observes ML experiment success, writes AGENTS-FEEDBACK.md (wiring, trackers, gaps, Try log), and recommends missing feedback. Use when starting ML work in a repo, before objective loops, or when the agent cannot tell if a run worked.
license: MIT
---

# Feedback setup

Creator skill. Build the **feedback map** so later skills have something concrete to read. Do not train models or run objective loops here.

Template: [`../../templates/AGENTS-FEEDBACK.md`](../../templates/AGENTS-FEEDBACK.md). Shared watchouts: [`../../references/things-to-watch.md`](../../references/things-to-watch.md).

## Steps

1. **Scan the repo** for existing feedback: train/eval entrypoints, metric prints, TensorBoard/W&B/MLflow, plot scripts, holdout definitions, CI model tests.
2. **Interview gaps** (ask user only what the tree cannot answer): primary business/metric goal; must-not-worsen slices; preferred tracker.
3. **Write or update `AGENTS-FEEDBACK.md`** at the consumer repo root from the template.
4. **Classify each signal** as primary, guardrail, or viz.
5. **Propose improvements** in Gaps & proposals — e.g. CV project with no plot grid; metrics only every epoch when step-level is needed; no holdout; no slice metrics; tracker present but unused.
6. **Ensure Try log table exists** (empty header row OK).

## Completion criteria

- [ ] `AGENTS-FEEDBACK.md` exists and lists at least one runnable feedback command/path **or** an explicit proposal for the minimum wiring to add
- [ ] Tracker field filled (`none` is valid)
- [ ] Gaps & proposals non-empty when wiring is thin
- [ ] User told what was written and what to add next

## Challenge

- Chat-only “we’ll just look at loss” is not a feedback map — demand a command or file path.
- Do not invent MLflow/W&B setup theater if a simple `results.tsv` + eval script is enough; do register a tracker if it already exists.

## Sources

- [Karpathy autoresearch](https://github.com/karpathy/autoresearch) — `program.md` / `results.tsv` separation
- [`../../references/sources.md`](../../references/sources.md)
