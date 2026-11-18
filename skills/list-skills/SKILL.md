---
name: list-skills
description: >-
  Lists ml-wisdom-skills with one-line purpose and when to use each. Use when
  the user asks what skills are available, how to use this pack, which skill to
  start with, or "what can you do" for ML/DL judgment workflows.
license: MIT
---

# list-skills

Utility router. Print the catalog below (do not load every other skill’s body).
Point the human at **one** next skill for their situation.

## How to answer

1. Show the tables (spine, then CV overlays, then utilities).
2. Ask or infer their situation; recommend **one** skill to run next.
3. If wiring is missing, prefer `wire-feedback` before loops.
4. Do not paste full procedures from other skills — name them and stop.

## Spine

| Skill | Does | Call when |
|-------|------|-----------|
| `wire-feedback` | Write short `AGENTS-FEEDBACK.md` router/config + scaffold `EXPERIMENTS.md` | New repo, stale wiring, agent can’t tell keep vs discard |
| `audit-data` | Data go/no-go before modeling | New task, label/data change, results smell wrong |
| `design-experiment` | Hypothesis, baseline ladder, one change | Planning runs, tempted to turn many knobs |
| `verify-training` | Correctness gates (init loss, tiny-batch overfit, …) | Before long trains / after pipeline changes |
| `diagnose-training` | Ordered probes when training is sick | NaN, can’t overfit, train↓ val flat — before HP thrashing |
| `validate-results` | Metrics, slices, Goodhart / metric–utility guardrails | Choosing/defending a metric; scores vs intended use |
| `run-objective-loop` | Scoped keep/discard ratchet + `program.md` / `results.tsv` | Bounded iteration toward a measurable goal |

## CV overlays

| Skill | Does | Call when |
|-------|------|-----------|
| `cv-data-quality` | Vision data/label/leakage audit | Image/video datasets (with `audit-data`) |
| `cv-augmentation` | Aug policy after verify gates | Adding/tuning augs; train/serve mismatch |
| `cv-transfer` | Freeze/finetune / backbone judgment | Choosing or finetuning a pretrained vision model |
| `cv-error-analysis` | FP/FN grids, soft edges, seams → attribution | After trusted eval; metrics fine but overlays/mosaic bad |

## Utilities

| Skill | Does | Call when |
|-------|------|-----------|
| `list-skills` | This catalog | “What skills?” / “Where do I start?” |

## Typical order

`wire-feedback` → `audit-data` → `design-experiment` → `verify-training` → (train) → `validate-results` / `diagnose-training` → `run-objective-loop` if needed.

## Artifacts (consumer repo)

| File | Role |
|------|------|
| `AGENTS-FEEDBACK.md` | Short config/router — not an experiment dump |
| `EXPERIMENTS.md` | Append-only experiment log |
| `program.md` + `results.tsv` | One objective-loop contract + dense ledger |

## Out of scope

- Implementing other skills’ procedures
- Hub/CLI how-tos (compose with HF / Ultralytics / etc.)
- Proposed-but-unshipped skills (`audit-repo`, `design-loss`, …) — omit unless they exist under `skills/`
