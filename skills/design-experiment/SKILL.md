---
name: design-experiment
description: >-
  Enforces hypothesis-driven ML experiments, one change at a time, baseline
  ladder, and keep/discard thinking before tuning or scaling complexity. Use
  when planning runs, tempted to change many knobs, skipping baselines, or
  starting an objective loop. Trigger terms: baseline ladder, one complexity at
  a time, hypothesis, keep/discard, mutable surface, when not to tune.
license: MIT
---

# design-experiment

Run experiments like a skeptic with a lab notebook: **hypothesis → one change → measure → keep/discard**. Climb the **baseline ladder**; turn the complexity dial one click at a time.

Compose with MLflow/W&B/HF tool skills for logging APIs. Reread `AGENTS-FEEDBACK.md` for signals; do not invent metrics.

## Gate: hypothesis + single change + ladder position

**Pass**: written hypothesis; exactly one intentional change (or an explicit bundle the human approved); current step on the baseline ladder named; keep/discard rule tied to a **primary signal** (and known **guardrails**).  
**Waiver**: human accepts multi-change exploration in writing (time-box it; still log).

## Baseline ladder

Climb in order; do not skip unless waived:

1. **Chance / bias** — random or majority-class / mean predictor
2. **Linear / shallow** — logistic/linear, tiny GBM, linear probe on frozen features
3. **Simple net** — small MLP/CNN/Transformer with defaults
4. **Human-rough** — spot-check: would a person beat this on a sample?
5. **Heavier model / pretrain / exotic SSL** — only after lower rungs clear

**2019 recipe nuance**: skepticism of unsupervised pretrain is softer in 2024+ foundation-model land — fine-tuning a known pretrained model can be the right “simple net.” Still: do **not** chase custom exotic unsupervised schemes before a supervised or standard fine-tune baseline and a working feedback map.

## Steps

### 1. Read the feedback map

Open `AGENTS-FEEDBACK.md`. If missing/stale → run `wire-feedback` first.

**Done when**: primary + guardrail signals are named for this experiment.

### 2. State hypothesis and mutable surface

Write:

- **Hypothesis**: what should improve and why
- **Mutable surface**: files/knobs allowed to change; what is forbidden (raw data, unrelated packages, silent split edits)
- **Keep rule**: primary signal threshold or comparison to baseline
- **Discard rule**: revert when primary fails or a guardrail trips

**Done when**: another agent could execute the run without asking “what are we testing?”

### 3. One change at a time

Make a single intentional delta (LR **or** aug **or** architecture width — not all three).

If debugging correctness, prefer `verify-training` gates before any tuning.

**Done when**: `git diff` (or equivalent) matches the claimed change.

### 4. When NOT to tune

Stop and fix foundations instead of sweeping hyperparameters when:

- Train loss does not decrease (correctness)
- Data audit is **no-go** (`audit-data`)
- Feedback too coarse to tell keep from discard
- You are below the ladder rung you are pretending to optimize

**Done when**: you either tune with a clean ladder position or switch to a foundation fix.

### 5. Run, record, keep/discard

Measure via the feedback map. Append an entry to `EXPERIMENTS.md` (not `AGENTS-FEEDBACK.md`).

- **Keep**: land the change; note why signals support it
- **Discard**: revert; note what you learned

For autonomous ratchets, point to an **objective loop** with `program.md` + `results.tsv` (goal, mutable surface, keep rules, append-only ledger). This skill sets discipline; the loop skill executes the ratchet.

**Done when**: `EXPERIMENTS.md` entry exists; working tree matches keep/discard decision.

## Complexity dial

Increase only one of: data complexity, model capacity, training trickiness, or optimization difficulty — per experiment. Visualize and verify at each click (Karpathy).

## Challenge stance

- “Let’s switch backbone, LR schedule, and loss together” → split or get a waiver.
- Tuning before majority/linear baselines → send back down the ladder.
- Primary up, guardrail on fire → discard or waiver; do not celebrate.

## Related

- Shared traps: [`../../references/things-to-watch.md`](../../references/things-to-watch.md)
- Ladder detail: [`references/baseline-ladder.md`](references/baseline-ladder.md)
- Correctness before scale: `verify-training`

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — baselines; one complexity at a time; don’t get fancy early.
- Pack sources: [`../../references/sources.md`](../../references/sources.md)
