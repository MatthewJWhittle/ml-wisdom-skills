# Feedback inventory checklist

Use while scanning a consumer repo for `wire-feedback`.

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

## Vision / stitched products (when applicable)

- [ ] Prediction overlay / sample grid (image + label + pred)
- [ ] Edge/boundary check if outlines matter
- [ ] Precision/recall at the real deploy threshold (not only mAP)
- [ ] Mosaic or seam check if tiles are stitched
- [ ] Short “good enough” rubric path (optional)

## Quality bar for each source

- Can an agent run it without tribal knowledge?
- Does it separate keep vs discard for typical one-change experiments?
- Is there a guardrail when the main score can lie (leakage, bad slice, OOM,
  soft edges, mosaic seams)?
