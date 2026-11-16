---
name: cv-transfer
description: Judges CV transfer learning — when to transfer, freeze vs unfreeze, head vs backbone LRs, domain gap, input size and norm stats. Use when choosing or finetuning a pretrained vision backbone, debugging finetune instability, or deciding not to train from scratch.
license: MIT
---

# CV transfer learning

**Overlay** on `train-verify` and `experiment-discipline`. Load spines for correctness gates and one-change-at-a-time experiments; this skill for backbone/finetune policy. Compose with Hub/Ultralytics/torchvision weight loaders — do not invent exotic towers before a standard pretrained baseline works (“don’t be a hero,” [Karpathy recipe](https://karpathy.github.io/2019/04/25/recipe/)).

Canonical framing: [CS231n transfer learning](https://cs231n.github.io/transfer-learning/).

## Default stance

1. **Start from a strong pretrained backbone** — ImageNet-supervised or a modern SSL/foundation vision backbone — before training from scratch or inventing custom pretraining.
2. **Karpathy’s old “unsupervised pretrain doesn’t help CV” is outdated** for today’s foundation/SSL backbones. The enduring rule remains: *do not* invent exotic pretraining before a standard supervised or SSL checkpoint + finetune path is proven.
3. Match **input size, interpolation, and normalization stats** to how the backbone was pretrained unless you deliberately adapt and re-verify.

## When to transfer

| Data size vs domain gap | Prefer |
|-------------------------|--------|
| Small, similar to pretrain | Freeze backbone; train head / linear probe |
| Large, similar | Finetune deeper; often full net with low backbone LR |
| Small, large domain gap | Freeze or partial freeze; probe earlier features; expect more data/aug need |
| Large, large domain gap | Init from pretrained anyway; full finetune common; scratch only if justified |

## Freeze → unfreeze recipe

```
1. Replace head for task; freeze backbone
2. Train head to stability (higher LR on head)
3. Unfreeze top blocks or full backbone
4. Finetune with lower LR on backbone than head
5. Only then try architecture heroics
```

Details: [references/finetune-recipe.md](references/finetune-recipe.md).

## LR & init tips

- Random **head** LR ≫ finetuned **backbone** LR (CS231n practical advice).
- Verify loss@init after attaching a new head (`train-verify`).
- Watch BN: freeze-BN vs train-BN is a deliberate choice under small batches / domain shift.

## Domain shift checks

- Pretrain domain (natural photos) vs yours (medical, industrial, sat, UI screenshots).
- Color/resolution statistics; grayscale vs RGB; multi-spectral ≠ RGB ImageNet.
- If gap is huge, pretrained init still often wins early — measure vs a tiny-from-scratch baseline before abandoning transfer.

## Done when

- Chosen backbone + freeze/unfreeze stage stated and logged.
- Norm/resize contract matches weights (or waiver).
- Head-only then unfreeze path tried before custom architectures.

## Out of scope

Aug policy → `cv-augmentation`. Post-hoc error grids → `cv-error-analysis`.
