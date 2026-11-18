---
name: diagnose-training
description: >-
  Diagnoses sick neural training with ordered symptom trees and a fixed probe
  sequence (shapes → labels → loss → grads → optimizer). Use when loss is NaN/Inf,
  the model cannot overfit a tiny batch, train loss drops while val is flat, ReLUs
  die, gradients vanish/explode, LR looks wrong (jitter or crawl), param groups
  misbehave, or shape/broadcast bugs mix the batch dimension — before random
  hyperparameter thrashing.
license: MIT
---

# diagnose-training

When training is **sick**, stop tuning. Walk symptom trees, then probes, then one
fix. Inspired by Karpathy’s recipe, CS231n neural-networks-3, and FSDL
troubleshooting — judgment, not trainer CLI docs.

**Gate rule:** every hard stop is **pass** or an explicit **human waiver**. Soft
advice is not a gate.

Also load `verify-training` if correctness gates (init loss, tiny-batch overfit)
have never been run. Cross-check [things-to-watch](../../references/things-to-watch.md).

## Stance

**Challenge** random hyperparameter thrashing. Changing LR/width/aug while a bug
is open is cargo-cult. Name the failed observation, pick one hypothesis, run one
probe, then one change.

## Probe order (always)

Complete in order unless a later probe already proves an earlier failure:

1. **Shapes** — batch, channel, spatial/seq dims; silent broadcast; train vs eval layout.
2. **Labels** — class IDs, one-hot vs index, ignore index, decode off-by-one, shuffle alignment.
3. **Loss** — finite; scale at init vs analytical expectation; reduction consistent with LR lore.
4. **Grads** — finite; reach the right modules; not all ~0 on large groups.
5. **Optimizer** — param groups, LR/schedules, WD on biases/BN, frozen vs trainable mismatch.

**Done when:** the first failing probe is named, evidence is recorded (log
snippet, tensor stats, or plot path), and either a fix is proposed or a human
waiver is requested.

Detail: [probe-order.md](references/probe-order.md).

## Symptom trees (enter by observation)

| Observation | Enter |
|-------------|--------|
| Cannot overfit tiny batch (aug/dropout/WD off) | [A](references/symptom-trees.md#a-cannot-overfit-tiny-batch) |
| Train ↓, val flat/↑ on every slice | [B](references/symptom-trees.md#b-train--val-flat) |
| NaN / Inf in loss or activations | [C](references/symptom-trees.md#c-naninf) |
| Dead ReLUs / collapsed activations | [D](references/symptom-trees.md#d-dead-relus) |
| Grad flow / param-group anomalies | [E](references/symptom-trees.md#e-grad-flow--param-groups) |
| Loss jitter (LR high) or crawl (LR low) | [F](references/symptom-trees.md#f-lr-too-high--too-low) |
| Suspicious “trains but ignores neighbors” | [G](references/symptom-trees.md#g-shape--broadcast--batch-mixing) |

**Done when:** leaf cause is identified or the tree ends in “escalate / waiver”
with what was ruled out.

## Grad-to-input probe (batch-mixing)

If shapes look “fine” but learning is weird: require grad w.r.t. input (or early
features) for one example; neighbors in the batch must not produce correlated
grads through a broadcast path. See tree G.

## After a fix

1. Re-run the **earliest failed probe**, then the original symptom check.
2. Only then resume normal training or a `run-objective-loop`.
3. Log briefly in `EXPERIMENTS.md` (hypothesis → evidence → fix).

## Out of scope

Trainer flag encyclopedias and “try a new architecture” before the probe order clears.

## Sources

- [Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/)
- [CS231n neural-networks-3](https://cs231n.github.io/neural-networks-3/)
- [FSDL 2021 L7](https://fullstackdeeplearning.com/spring2021/lecture-7/)
