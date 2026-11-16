---
name: objective-loop
description: Runs a scoped ML objective loop — mutable-surface interview, program.md contract, fixed-budget experiments, results.tsv with reasoning traces, multi-signal keep/discard. Use when the user wants autoresearch-style iteration toward a measurable goal with feedback.
license: MIT
---

# Objective loop

Autoresearch **mechanics** + multi-signal **judgment**. Not unbounded repo editing.

Templates: [`../../templates/program.md`](../../templates/program.md), [`../../templates/results.tsv`](../../templates/results.tsv). Requires feedback map: `AGENTS-FEEDBACK.md` (run `feedback-setup` or waiver).

## Phase A — Scope interview (before any edit)

Confirm with the user:

1. **Objective** + primary signal + guardrails  
2. **Mutable surface** — hparams only / one file / recipe / feature code  
3. **Bans** — raw data, secrets, untracked datasets, evaluator code (default)  
4. **Git branch** for the loop  
5. **Budget** per experiment + stop rule  

Refuse to start without these (or an explicit waiver).

## Phase B — Contract

1. Write `program.md` from the template.  
2. Ensure `results.tsv` header exists.  
3. Confirm feedback commands from `AGENTS-FEEDBACK.md`.  
4. If a tracker is configured, note how to mirror runs.

## Phase C — Ratchet

Each experiment:

1. Hypothesis (one change).  
2. Edit only within mutable surface; commit on the loop branch.  
3. Run via feedback map; redirect logs to a file (don’t flood context).  
4. Parse primary + guardrails.  
5. Append `results.tsv` row: commit, metrics, status, **hypothesis, change, interpretation, rationale**.  
6. **Keep** if primary improves **and** no guardrail fails; else **reset** to previous keep.  
7. Crashes: fix typos quickly; if idea is broken, log `crash` and discard.

## Safety (ML-specific)

- Never delete or rewrite datasets/labels to improve scores.  
- Evaluator / metric definition is **immutable** during the loop.  
- Ask before expanding mutable surface or destructive git operations.  
- Comparable budgets each run (Karpathy fixed wall-clock idea).

## Phase D — Close

Append a condensed Try log row to `AGENTS-FEEDBACK.md`. Summarize path from first to last keep for the user.

## Challenge

- Blind metric climbing with failed guardrails → discard.  
- “Never stop” overnight runs only if the user explicitly wants unattended mode; otherwise checkpoint and report.

## Completion criteria

- [ ] `program.md` + scoped mutable surface  
- [ ] At least one logged experiment row with reasoning fields **or** user stopped early with summary  
- [ ] Try log updated on close  

## Sources

- [Karpathy autoresearch `program.md`](https://github.com/karpathy/autoresearch/blob/master/program.md)
- [Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/)
- [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
