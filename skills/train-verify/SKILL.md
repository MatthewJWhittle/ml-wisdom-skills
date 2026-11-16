---
name: train-verify
description: Runs deep learning correctness gates before scaling training — loss at init, final-layer init, tiny-batch overfit, input-independent baseline, pre-model visualization. Use when a new training loop is written, before long runs, or when results are untrustworthy.
license: MIT
---

# Train verify

Silent failure is the default. Earn the right to train longer.

Also see FSDL **memorization / overfit_batches** smoke tests and [`../../references/things-to-watch.md`](../../references/things-to-watch.md).

## Gates (each: pass or explicit human waiver)

| # | Gate | Pass criterion |
|---|------|----------------|
| 1 | Fixed seed | Rerun matches (or documented nondeterminism) |
| 2 | Simplify | Aug / dropout / WD off for overfit test |
| 3 | Loss @ init | Near analytical value (e.g. softmax ≈ `-log(1/n_classes)`) |
| 4 | Final-layer init | Bias matches class prior / regression mean as appropriate |
| 5 | Input-independent | Zeroed inputs worse than real inputs |
| 6 | Overfit tiny batch | Few examples → ~minimum achievable loss; preds align with labels |
| 7 | Pre-model viz | Decode batch **immediately before** forward |
| 8 | Train loss ↓ | Capacity bump decreases train loss when underfitting |

## Steps

1. Run gates in order; stop on fail and open `train-diagnose` if stuck.
2. Record passes/fails/waivers (chat summary + Try log row if useful).
3. Only then enable aug/schedules/longer training (`experiment-discipline`).

## Challenge

- “It kind of trains” is not a pass on gate 6.
- Do not trust LR decay defaults copied from ImageNet epoch schedules.
- Early Adam ~3e-4 is a safe **starting** optimizer choice, not a final claim.

## Completion criteria

- [ ] Gate table filled (pass / fail / waiver + reason)
- [ ] Failures have a next diagnostic action

## Sources

- [Karpathy recipe §2](https://karpathy.github.io/2019/04/25/recipe/)
- [FSDL L3 Troubleshooting & Testing](https://fullstackdeeplearning.com/course/2022/lecture-3-troubleshooting-and-testing/)
- [CS231n neural-networks-3](https://cs231n.github.io/neural-networks-3/)
