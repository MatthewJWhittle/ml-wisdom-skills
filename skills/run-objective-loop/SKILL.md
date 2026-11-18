---
name: run-objective-loop
description: >-
  Runs a scoped Karpathy-style experiment ratchet with multi-signal keep rules
  (primary improves and no guardrail fails), program.md contract, and
  results.tsv reasoning traces. Use when iterating model/training changes under
  a fixed budget, starting an autoresearch-like loop, or needing keep/discard
  discipline with a feedback map — after AGENTS-FEEDBACK.md exists or is waived.
license: MIT
---

# run-objective-loop

A bounded **change → run → record → keep|reset** ratchet (Karpathy autoresearch
mechanics) driven by a **primary signal plus guardrails**, not a single blind
metric.

Templates: [program.md](../../templates/program.md),
[results.tsv](../../templates/results.tsv),
[AGENTS-FEEDBACK.md](../../templates/AGENTS-FEEDBACK.md).
Watch list: [things-to-watch](../../references/things-to-watch.md).

## 0. Preconditions

| Requirement | Gate |
|-------------|------|
| `AGENTS-FEEDBACK.md` present and readable | **Pass** or **human waiver** (waiver must say how to run eval this loop) |
| Training not currently “sick” | If sick, run `diagnose-training` first — **pass**/waiver before looping |
| Goal metrics make sense | Load `validate-results` when choosing primary/guardrails |

## 1. Scope interview (FIRST — before code edits)

Ask and record in `program.md`:

1. **Mutable surface** — pick one: hparams only / one named file / training
   recipes / feature or pipeline code / explicitly expanded other.
2. **Bans** — raw datasets, secrets, untracked data dirs, silent label rewrites,
   evaluator edits (default).
3. **Git** — default work on a **dedicated branch**; ask before destructive or
   out-of-scope actions (shared-branch reset, deleting data, force-push).
4. **Budget** — fixed wall-clock or step/epoch budget per try (comparability).

**Done when:** human confirms scope (or a prior program.md is re-confirmed).
Do not start the loop without it.

## 2. Write the contract

Create/update `program.md` with at least:

- Goal (one sentence)
- Primary signal (name, direction, how to read from feedback map)
- Guardrail signals (fail conditions)
- Keep rule, budget, mutable surface, bans
- Logging: `results.tsv` + tracker mirror if the feedback map names MLflow/W&B/etc.

Field list matches [program.md](../../templates/program.md).

**Done when:** file exists and matches the interview.

## 3. Loop

Repeat until stop condition in `program.md` or human interrupt:

1. **One change** inside the mutable surface (one hypothesis).
2. **Commit** on the loop branch.
3. **Run** via the feedback map command(s); respect the fixed budget (kill/over-
   budget → treat as crash).
4. **Parse** primary + guardrails (and any required plots).
5. **Append** a row to `results.tsv` with reasoning-trace fields (see below).
6. **Keep or reset**
   - **Keep** iff primary improves **and** no guardrail fails.
   - Else **discard**: reset code to last keep; leave the TSV row as `discard`/`crash`.
7. Mirror metrics to MLflow/W&B **only if** the feedback map says so — the TSV
   remains the universal agent ledger.

Crashes: fix typos/imports quickly and retry; if the idea is broken, log `crash`
and move on.

## 4. Keep rule (multi-signal)

``
keep ⇔ primary_improved ∧ ¬any_guardrail_failed
``

Primary-only wins that trip [things-to-watch](../../references/things-to-watch.md)
items named as guardrails in `program.md` are discards. Complexity: prefer
simpler changes when primary deltas are tiny (Karpathy simplicity criterion).

**CV examples of guardrail vetoes:** boundary/HD95 worse; operating-point PR
down despite mAP up; mosaic/seam disagreement up; intended-use rubric fails —
even if Dice/IoU/accuracy improved ([ADR 0010](../../docs/adr/0010-metric-utility-gap.md)).

## 5. `results.tsv` schema

Header (see [template](../../templates/results.tsv)):

``
commit	primary	guardrails	status	hypothesis	change	interpretation	rationale	description
``

| Field | Content |
|-------|---------|
| commit | short hash |
| primary | numeric primary (agreed precision) |
| guardrails | compact pass/fail summary |
| status | `keep` / `discard` / `crash` |
| hypothesis | what you believed would happen |
| change | what you edited (mutable-surface scoped) |
| interpretation | how signals read vs expectation |
| rationale | why keep/discard/crash |
| description | one-line summary |

Usually **untracked**. Every attempt gets a row — including failures.

## 6. Loop end → experiment log

Append a **condensed** entry to `EXPERIMENTS.md` (path from `AGENTS-FEEDBACK.md`
if overridden). Use the entry template there (hypothesis, change, signals,
decision, link to `results.tsv`/tracker). Dense history stays in `results.tsv`.
Do **not** append narratives to `AGENTS-FEEDBACK.md`.

## 7. ML-iteration safety (hard)

Phrase as required behavior:

- **Preserve datasets** — never delete or overwrite raw/source data to “clean” a run.
- **Labels stay honest** — no silent label rewrites to lift the metric.
- **Evaluator immutable** — do not improve scores by editing the eval harness or
  test labels; expand scope only with explicit human approval.
- **Fixed budget** — same time/steps for comparable tries; note any intentional
  budget change as its own experiment.

## Out of scope

Unbounded “never stop” autonomy without a stop rule when the human expects
checkpoints; replacing project trackers; diagnosing broken training (use
`diagnose-training`).
