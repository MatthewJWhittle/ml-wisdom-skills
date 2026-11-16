# ml-wisdom-skills

Agent Skills that encode **ML / deep learning judgment** — experiment design, training verification, diagnostics, evaluation discipline, and bounded objective loops.

This is the difference between an agent that can write a training loop and one that can run a valid research process.

## Install

Requires a skills-compatible agent ([Agent Skills](https://agentskills.io/)).

```bash
npx skills add MatthewJWhittle/ml-wisdom-skills
```

Install a subset:

```bash
npx skills add MatthewJWhittle/ml-wisdom-skills --skill train-verify
```

## What’s inside

### Spine

| Skill | When |
|-------|------|
| [`feedback-setup`](skills/feedback-setup/SKILL.md) | Wire how the agent observes success; write `AGENTS-FEEDBACK.md` |
| [`data-judgment`](skills/data-judgment/SKILL.md) | Inspect data before modeling |
| [`experiment-discipline`](skills/experiment-discipline/SKILL.md) | Hypotheses, baselines, one change at a time |
| [`train-verify`](skills/train-verify/SKILL.md) | Correctness gates (loss@init, overfit tiny batch, …) |
| [`train-diagnose`](skills/train-diagnose/SKILL.md) | Decision trees when training is sick |
| [`evaluate`](skills/evaluate/SKILL.md) | Metrics, slices, Goodhart guardrails |
| [`objective-loop`](skills/objective-loop/SKILL.md) | Scoped ratchet iteration with reasoning traces |

### Computer vision overlays

| Skill | When |
|-------|------|
| [`cv-data-quality`](skills/cv-data-quality/SKILL.md) | Image/label quality & leakage |
| [`cv-augmentation`](skills/cv-augmentation/SKILL.md) | Augmentation after correctness |
| [`cv-transfer`](skills/cv-transfer/SKILL.md) | Transfer / fine-tune judgment |
| [`cv-error-analysis`](skills/cv-error-analysis/SKILL.md) | FP/FN grids, slices, next action |

## Consumer artifacts

Templates live in [`templates/`](templates/).

| File | Role |
|------|------|
| `AGENTS-FEEDBACK.md` | Standing feedback map + Try log |
| `program.md` | One objective-loop contract |
| `results.tsv` | Per-run ledger + reasoning fields |

## Not this repo

Hub/CLI trainers, YOLO pipelines, package encyclopedias — use [huggingface/skills](https://github.com/huggingface/skills), [ultralytics/skills](https://github.com/ultralytics/skills), etc. See [`references/sources.md`](references/sources.md).

## License

MIT — see [LICENSE](LICENSE).
