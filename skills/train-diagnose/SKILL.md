---
name: train-diagnose
description: Diagnoses sick deep learning training with ordered probes — shapes, labels, loss, grads, optimizer — for NaNs, failed overfit, train/val divergence, and silent bugs. Use when train-verify fails, loss explodes, or metrics make no sense.
license: MIT
---

# Train diagnose

Random hyperparameter thrashing is not diagnosis. Probe in order.

Detail tree: [`references/decision-tree.md`](references/decision-tree.md). Watchouts: [`../../references/things-to-watch.md`](../../references/things-to-watch.md).

## Probe order

1. **Shapes** — tensor ranks/sizes through the graph; annotate expected shapes.
2. **Labels** — alignment with inputs; class index range; augmentation label consistency.
3. **Loss** — correct loss for outputs (logits vs probs); reduction; init value.
4. **Grads** — finite norms; which params get grad; batch-mixing probe (loss on example i → grad only on input i).
5. **Optimizer** — param groups, LR not decayed to ~0, WD not applied wrongly to norms.

## Symptom → first checks

| Symptom | First checks |
|---------|----------------|
| Cannot overfit tiny batch | Labels shuffled? LR 0? Wrong loss? Aug still on? Mode train? |
| NaN/Inf | Grad spike; LR too high; bad init; AMP overflow; div by zero in metrics |
| Train ↓ val flat | Leakage? Too small model? Data bug? Need capacity then regularize (Karpathy overfit→regularize) |
| Val ≫ train suddenly | Aug mismatch; BN/dropout eval mode; different preprocess |
| “Works” but smells | Batch-mixing; eval on train; target leakage |

## Challenge

- No LR sweep until tiny-batch overfit passes or is waived.
- Fix data/loop bugs before architecture changes.

## Completion criteria

- [ ] Symptom named
- [ ] Probes run in order up to the failing layer
- [ ] Concrete fix or waiver + next experiment hypothesis

## Sources

- [Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/)
- [FSDL 2021 L7](https://fullstackdeeplearning.com/spring2021/lecture-7/)
- [CS231n neural-networks-3](https://cs231n.github.io/neural-networks-3/)
