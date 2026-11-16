# Baseline ladder (detail)

Use with `design-experiment`. Each rung is a **gate**: beat it (or waive) before climbing.

| Rung | Example | Pass idea |
|------|---------|-----------|
| Chance / bias | Uniform random; majority class; predict train mean | Metrics ≈ this → model not learning signal |
| Linear / shallow | LogReg, linear probe, small GBM | Beats chance on val; establishes feature usefulness |
| Simple net | Small CNN/MLP, default YOLO size, tiny Transformer | Beats linear; training dynamics healthy |
| Human-rough | Spot labels on a fixed sample | Catches label/task misspecification |
| Heavier / pretrained / custom SSL | Larger backbones, heavy aug, exotic pretrain | Only after lower rungs and `verify-training` |

## Classical ML

Same ladder: chance → simple linear/tree → tuned ensembles. Skip neural-specific rungs; do not skip chance/majority.

## Foundation models

Starting from a standard pretrained checkpoint counts as “simple net” if the head/train recipe is minimal. Custom SSL from scratch remains a high rung.
