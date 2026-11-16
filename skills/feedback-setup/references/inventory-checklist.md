# Feedback inventory checklist

Use while scanning a consumer repo for `feedback-setup`.

## Commands & scripts

- [ ] `Makefile` / `justfile` / `package.json` scripts that eval or plot
- [ ] `scripts/`, `tools/`, `notebooks/` that produce metrics or figures
- [ ] README “Evaluation” / “Repro” sections with copy-paste commands
- [ ] CI workflows that run tests on model quality (not only unit tests)

## Artifacts

- [ ] Metric files under `runs/`, `outputs/`, `lightning_logs/`, `mlruns/`
- [ ] Saved predictions, confusion matrices, embedding projections
- [ ] Dataset split manifests (`train.txt`, parquet partitions, group IDs)

## Trackers

- [ ] `mlflow` / `wandb` / `tensorboard` deps or config
- [ ] Existing experiment names, projects, or local store URIs
- [ ] Auth assumptions (env vars) — note them; do not invent secrets

## Quality bar for each source

- Runnable by an agent without tribal knowledge?
- Discriminates keep vs discard for typical one-change experiments?
- Has at least one **guardrail** if the primary can lie (leak, slice collapse, OOM)?
