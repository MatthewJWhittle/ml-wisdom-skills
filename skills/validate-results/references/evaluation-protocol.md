# Evaluation protocol

For a consequential comparison, record a few lines in the experiment log:

- **Claim and population** — what quantity the metric estimates and for which deployment cases.
- **Units and splits** — prediction unit, independent sampling or grouping unit, and which data served training, selection and final evaluation.
- **Comparison** — baseline and candidate on the same cases where possible. Record budget, inference preprocessing, deployment threshold, calibration when scores are used as confidence, and relevant guardrails or whole-product checks.
- **Uncertainty** — report variation across seeds or splits if it matters. Use intervals when sample size and dependence assumptions support them. For paired differences, resample matched cases or groups together. Explain how much evidence the smallest important slices supply.
- **Interpretation** — state whether the comparison is inconclusive, an exploratory improvement on selection data, or a supported improvement within the measured scope.

Independent classification images may support paired resampling by image. For repeated patient visits or site-level data, split and assess uncertainty at the independent patient or site level as appropriate to deployment. For overlapping crops, assess performance by held-out source image or recording and account for dependence between groups. Resampling individual pixels as independent will generally overstate precision. Sparse or dependent groups can make an interval unreliable.
