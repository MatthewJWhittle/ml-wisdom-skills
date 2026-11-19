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
npx skills add MatthewJWhittle/ml-wisdom-skills --skill verify-training
```

## What’s inside

### Spine

| Skill | When |
|-------|------|
| [`wire-feedback`](skills/wire-feedback/SKILL.md) | Wire how the agent observes success; write `AGENTS-FEEDBACK.md` |
| [`audit-data`](skills/audit-data/SKILL.md) | Inspect data before modeling |
| [`design-experiment`](skills/design-experiment/SKILL.md) | Diagnostic probes, fair comparisons, and bounded tuning |
| [`verify-training`](skills/verify-training/SKILL.md) | Correctness gates (loss@init, overfit tiny batch, …) |
| [`build-tiny-loop`](skills/build-tiny-loop/SKILL.md) | Build a fast, observable run using the real pipeline |
| [`clarify-ml-system`](skills/clarify-ml-system/SKILL.md) | Build a shared glossary for the task, model, loss or training mechanism |
| [`diagnose-training`](skills/diagnose-training/SKILL.md) | Decision trees when training is sick |
| [`validate-results`](skills/validate-results/SKILL.md) | Metrics, slices, Goodhart guardrails |
| [`run-objective-loop`](skills/run-objective-loop/SKILL.md) | Scoped ratchet iteration with reasoning traces |
| [`design-learning-objective`](skills/design-learning-objective/SKILL.md) | Align the target, supervision, head and loss |

### Computer vision overlays

| Skill | When |
|-------|------|
| [`cv-data-quality`](skills/cv-data-quality/SKILL.md) | Image/label quality & leakage |
| [`cv-augmentation`](skills/cv-augmentation/SKILL.md) | Augmentation after correctness |
| [`cv-transfer`](skills/cv-transfer/SKILL.md) | Transfer / fine-tune judgment |
| [`cv-error-analysis`](skills/cv-error-analysis/SKILL.md) | FP/FN grids, slices, next action |

### Utilities

| Skill | When |
|-------|------|
| [`list-skills`](skills/list-skills/SKILL.md) | “What skills?” / which to run next (thin router) |

## Consumer artifacts

Templates live in [`templates/`](templates/).

| File | Role |
|------|------|
| `AGENTS-FEEDBACK.md` | Router/config: sources, trackers, skill map |
| `EXPERIMENTS.md` | Append-only experiment log |
| `program.md` | One objective-loop contract |
| `results.tsv` | Per-run ledger + reasoning fields |

## Not this repo

Hub/CLI trainers, YOLO pipelines, package encyclopedias — use [huggingface/skills](https://github.com/huggingface/skills), [ultralytics/skills](https://github.com/ultralytics/skills), etc. See [`references/sources.md`](references/sources.md).

## Markdown maintenance

From the repository root, scan tracked Markdown for accidental two-backtick fence lines.

```bash
git grep -n -E '^[[:space:]]*`{2}([[:alnum:]_-]+)?[[:space:]]*$' -- '*.md'
```

No matches is the expected result. Review any matches before editing inline code.

## License

MIT — see [LICENSE](LICENSE).
