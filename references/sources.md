# Sources

Enduring ML/DL judgment this pack cites. Prefer primary sources; skill bodies keep principles short.

## Core

- [A Recipe for Training Neural Networks](https://karpathy.github.io/2019/04/25/recipe/) — Andrej Karpathy (2019). Become one with the data; baselines; overfit a tiny batch; verify loss at init; visualize before the model; one complexity at a time.
- [Yes you should understand backprop](https://karpathy.github.io/2016/09/01/critic/) — Andrej Karpathy (2016). Gradient intuition as a debugging lens.
- [A Recipe for Training Neural Networks (HN / discussion archives)](https://news.ycombinator.com/item?id=19752276) — community caveats on recipe age.

## Nuance (post-2019)

- Unsupervised / self-supervised pretraining skepticism in the 2019 recipe is **more nuanced in 2024+**: foundation models and SSL often win when labeled data is scarce. Still: do **not** chase exotic unsupervised schemes before a supervised (or fine-tune) baseline and a working feedback map.
- Autoresearch / objective-loop patterns: keep a durable try log and explicit mutable surface; do not reduce judgment to a single blind metric.

## Compose with (tool skills, not this repo)

- Hugging Face Skills / Hub / `transformers` / Trainer workflows
- Ultralytics YOLO training & eval CLIs
- MLflow / W&B experiment tracking
- Lightning / Accelerate / similar trainers

This repo owns **judgment**; those repos own **how to invoke the tool**.
