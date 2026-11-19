# Sources

Enduring ML/DL judgment this pack cites. Prefer primary sources; skill bodies keep principles short.

## Core

- [Domain Modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md) — Matt Pocock on testing shared terms against concrete scenarios and updating the project glossary as definitions are agreed.
- [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — Andrej Karpathy (2019). Become one with the data; baselines; overfit a tiny batch; verify loss at init; visualize before the model; one complexity at a time.
- [Yes you should understand backprop](https://karpathy.github.io/2016/09/01/critic/) — Andrej Karpathy (2016). Gradient intuition as a debugging lens.
- [Understanding deep learning requires rethinking generalization](https://arxiv.org/abs/1611.03530) — Zhang et al. (ICLR 2017). Networks can fit training data with random labels or fixed random inputs.
- [CS231n: Learning and Evaluation](https://cs231n.github.io/neural-networks-3/) — initial-loss sanity checks and numerical gradient checks.
- [A Recipe for Training Neural Networks (HN / discussion archives)](https://news.ycombinator.com/item?id=19752276) — community caveats on recipe age.

- [Google Deep Learning Tuning Playbook](https://developers.google.com/machine-learning/guides/deep-learning-tuning-playbook/scientific) — scientific, nuisance and fixed hyperparameters.
- [Google ML Problem Framing](https://developers.google.com/machine-learning/problem-framing/problem-framing) — connect prediction tasks, outputs and success criteria.
- [Cross-validation strategies for structured data](https://www.biom.uni-freiburg.de/mitarbeiter/dormann/roberts-et-al-2017-ecography.pdf/at_download/file) — Roberts et al. on matching blocked evaluation to dependence and the prediction task.

## Nuance (post-2019)

- Unsupervised / self-supervised pretraining skepticism in the 2019 recipe is **more nuanced in 2024+**: foundation models and SSL often win when labeled data is scarce. Still: do **not** chase exotic unsupervised schemes before a supervised (or fine-tune) baseline and a working feedback map.
- Autoresearch / run-objective-loop patterns: keep a durable `EXPERIMENTS.md` log and explicit mutable surface; do not reduce judgment to a single blind metric.

## Worked examples

- [Finite-Sample Equivalence in Statistical Models for Presence-Only Data](https://pmc.ncbi.nlm.nih.gov/articles/PMC4258396/) — Fithian and Hastie on what presence/background observations support under sampling bias.

## Compose with (tool skills, not this repo)

- Hugging Face Skills / Hub / `transformers` / Trainer workflows
- Ultralytics YOLO training & eval CLIs
- MLflow / W&B experiment tracking
- Lightning / Accelerate / similar trainers

This repo owns **judgment**; those repos own **how to invoke the tool**.
