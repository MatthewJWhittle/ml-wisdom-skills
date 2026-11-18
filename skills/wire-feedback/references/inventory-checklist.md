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

## CV / composition (when applicable)

- [ ] Pred overlay / sample grid script (image + GT + pred)
- [ ] Boundary or surface metric if contours matter for use
- [ ] Operating-point PR (not only mAP) if detection ships at a threshold
- [ ] Mosaic / AOI / seam check if inference tiles are stitched
- [ ] Intended-use rubric path registered (optional but valuable)

## Quality bar for each source

- Runnable by an agent without tribal knowledge?
- Discriminates keep vs discard for typical one-change experiments?
- Has at least one **guardrail** if the primary can lie (leak, slice collapse, OOM, soft boundaries, mosaic seams)?
