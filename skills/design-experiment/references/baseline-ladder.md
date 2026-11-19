# Baseline options

Choose comparators that answer the study question. A baseline ladder is a menu of increasingly capable references.

- **Input-independent reference** such as a train-set prior, mean or seasonal rule to calibrate the score.
- **Simple learned model** such as a linear model, shallow trees, or a small network to check whether extra complexity buys anything.
- **Standard pretrained model** with a linear probe or straightforward fine-tune when existing representations are relevant.
- **Incumbent or human comparator** when deployment value is the question. Measure on comparable cases with the same decision and scoring rules.

A small photo dataset may justify comparing a frozen pretrained encoder plus linear head with the incumbent. In multispectral imagery, check the checkpoint's channel and preprocessing contract. A compatible pretrained model with partial fine-tuning or a small native-channel model may be the more credible reference. State what each comparison controls and why further baselines would add little within the budget.
