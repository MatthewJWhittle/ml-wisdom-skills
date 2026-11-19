---
name: audit-data
description: >-
  Audits datasets before modeling (distributions, duplicates, label noise,
  imbalance, leakage, human decision cues; for tensors/images, raw samples and
  model inputs). Use when starting a new task, changing data or labels, before
  the first serious train, or when results look too good or too noisy. Trigger
  terms: become one with the data, data audit, go/no-go, label noise, leakage,
  class imbalance.
license: MIT
---

# audit-data

**Become one with the data** before modeling. Output a short written audit and an explicit **go / no-go** for modeling.

Default context: deep learning / neural nets; classical ML uses the same judgment where it applies. Compose with HF datasets / Ultralytics data CLIs for loading — this skill owns **what to notice**, not loader syntax.

## Gate: audit + go/no-go

**Pass**: audit written (see schema); go/no-go stated with evidence; if no-go, blocking issues listed.  
**Waiver**: human explicitly waives a named issue (e.g. “known label noise in class X; proceed”).

Do not start architecture search or objective loops on a **no-go** without waiver.

## Steps

### 1. Scope the decision

Name the prediction task in one sentence and what a **competent human** would look at to label one example.

**Done when**: human cues are listed (pixels? metadata? temporal context?). If the model cannot see those cues, flag representation risk.

### 2. Look at raw examples

Sample positives, negatives, rares, and “hard” cases from disk / tables — not only summary stats.

Check: corruption, obvious mislabels, near-duplicates, empty/broken inputs, surprising class boundaries.

**Done when**: you have viewed a stratified handful (order-of tens) and noted patterns in writing.

### 3. Look at what enters the model

For tensors / images / audio: inspect **after** decode, resize, normalize, augment-off crop — the tensor (or equivalent) that would hit `forward`.

Challenge any pipeline where raw looks fine but model input is silently broken (channel order, clipped masks, NaNs, wrong ROI).

**Done when**: at least one batch visualization or value sanity check exists for the training path.

### 4. Distribution & hygiene scan

Run through:

| Check | Ask |
|-------|-----|
| Class / target distribution | Imbalance? Rare slices that metrics will hide? |
| Duplicates | Exact or near-dup across splits? |
| Label noise | Systematic confusions a human would catch? |
| Missingness | Nulls, -1 sentinels, empty strings treated as signal? |
| Leakage risks | Same ID/group/time/scene in train and val? Target encoded into features? |

See also [`../../references/things-to-watch.md`](../../references/things-to-watch.md).

**Done when**: each row is pass / fail / unknown-with-plan.

### 5. Write the audit + decide

Produce a short audit (bullets fine):

```text
Task:
Human cues:
Raw sample notes:
Model-input notes:
Distributions / imbalance:
Duplicates / leakage:
Label quality:
GO | NO-GO — rationale:
Waivers (if any):
```

**GO** only if nothing blocking remains (or each blocker has a human **waiver**).  
**NO-GO** if leakage, broken inputs, or label chaos would make training metrics meaningless.

**Done when**: audit is saved where the project will find it (PR note, `AGENTS-FEEDBACK.md` pointer, or `docs/data-audit.md`).

## Challenge stance

- “We’ll clean labels after the first big train” → no-go until a tiny trusted slice exists.
- Stats-only comfort without viewing samples → incomplete; finish steps 2–3.
- Chasing exotic unsupervised pretrain because labels are messy → still prefer a supervised/fine-tune baseline on a cleaned slice first (2019 recipe skepticism is softer in the foundation-model era, see design-experiment for choosing credible comparators).

## Related

- Wire how success is measured: `wire-feedback` → `AGENTS-FEEDBACK.md`
- Before scaling train: `verify-training`
- Deeper prompts: [`references/audit-prompts.md`](references/audit-prompts.md)

## Sources

- Karpathy, [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — become one with the data; visualize before the model.
- Pack sources: [`../../references/sources.md`](../../references/sources.md)
