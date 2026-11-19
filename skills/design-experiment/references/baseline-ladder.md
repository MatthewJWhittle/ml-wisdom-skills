# Baseline options

Use with `design-experiment`. Pick the smallest set that can answer the question. For each comparator, name what it controls and what its result would change. Use the same held-out cases, metric and decision rule where applicable.

| Comparator | Example | What it tells you |
|------------|---------|-------------------|
| No-signal reference | Random predictor, training-set majority class or mean | Puts the score in context. A tie calls for inspecting the model, labels and metric. |
| Linear or shallow model | Logistic regression, frozen-feature linear probe, small boosted tree | Tests how much the features explain with a simple model. If performance is comparable, extra complexity needs a separate justification. |
| Small neural model | Small CNN, MLP or Transformer on a verified pipeline | Tests whether the data and objective support useful learning before a more expensive model comparison. |
| Standard pretrained model | Frozen probe, partial or direct fine-tune | Establishes what a routine transfer recipe can achieve when the checkpoint is a good fit. |
| Incumbent or human check | Current production rule or human review of fixed cases | Anchors deployment utility. A rough human spot-check can also expose ambiguous labels or an impossible task. |

## Classical ML

Linear or shallow tree models are useful references for a tuned ensemble. A no-signal reference helps interpret metrics that may look strong under imbalance.

## Pretrained models

A small in-domain image dataset can start with a frozen encoder and linear head. In multispectral imagery, check channel compatibility and preprocessing. Compare an adapted compatible checkpoint with a small native-channel model when the domain gap matters. Establish a standard supervised or transfer reference before investing in custom pretraining if either is credible for the task.
