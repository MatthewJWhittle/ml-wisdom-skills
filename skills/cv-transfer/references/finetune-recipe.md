# Finetune recipe (CV)

## Options

Pick a starting point based on data, domain gap and compute. Use these as stages only when gradual unfreezing helps isolate instability or feature reuse.

1. **Linear probe / new head** — backbone frozen; confirms features transfer at all.
2. **Partial unfreeze** — last stages / top blocks; moderate backbone LR.
3. **Full finetune** — entire net; lowest backbone LR; optional discriminative LRs by stage.
4. **Longer schedule / resolution bump** — after the chosen short run is stable.

## Hyperparameter heuristics

- Head LR: often 10×–100× backbone LR when unfrozen together.
- AdamW / cosine / WSD: fine after the loop is trustworthy; early on, stable Adam-ish defaults are enough (see repo `references/sources.md`).
- Disable foreign ImageNet epoch decay schedules that zero LR too early on small datasets.

## Input contract

| Setting | Rule |
|---------|------|
| Mean/std | Use the checkpoint’s documented stats |
| Resize / crop | Same family as pretraining (short-side resize + center crop, letterbox, etc.) |
| Color order | RGB vs BGR must match weights |
| Resolution | Start at pretrain size; increase later as a single experiment |

## SSL / foundation backbones

- Prefer well-known public checkpoints over ad-hoc contrastive runs on tiny private data.
- Read whether the checkpoint expects a specific projection head discard and pecularities (e.g. patch size, register tokens).
- Still run `verify-training` after wiring — wrong norm or missed interpolate is silent.

## When scratch can win

Rare early: huge in-domain data + large gap + compute. Even then, pretrained init is the baseline to beat, not a skipped step.
