# Model card: adult / logreg

*Logistic regression (interpretable).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: yes
- Hyperparameters: `{"C": 1.0, "max_iter": 2000, "random_state": null}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 0.977, intercept -0.0037)

## Intended use

- Primary: Demonstration and evaluation of explanation, fairness and uncertainty tooling on a public benchmark.
- Out of scope: Any real decision about people. These models are not validated for deployment.

## Training data

- Dataset: **Adult / Census Income (1994)** (real public data)
- Source: OpenML dataset 'adult' (data id 1590), originally UCI Machine Learning Repository, fetched with scikit-learn fetch_openml
- Version: OpenML version 2; raw table sha256 d273d956c28eba72
- Licence: Public (UCI repository, CC BY 4.0 for the Adult dataset)
- Rows: 48842, features: 9
- Content hash (sha256): `d457680936d68aac08cf40dc2bca4ba28c1a8d1f1ff3afbcd0e3baa9b64f0d8e`
- Target: income (positive = income above 50K per year (favourable outcome))
- Sensitive attribute: sex (Female/Male, as recorded in 1994) (audit only; never a model input)

Splits (stratified on the label; one fixed seed): train 26863, calibration 7326, conformal 7326, test 7327.

## Performance (held-out test split)

| n | accuracy | auc | log_loss | brier | ece | ece_raw | positive_rate | base_rate |
|---|---|---|---|---|---|---|---|---|
| 7327 | 0.8481 | 0.9004 | 0.3277 | 0.1047 | 0.0145 | 0.0136 | 0.1812 | 0.2393 |

## Fairness and calibration by subgroup

| group | n | base_rate | selection_rate | tpr | fpr | accuracy | mean_predicted | ece | brier |
|---|---|---|---|---|---|---|---|---|---|
| Female | 2427 | 0.1059 | 0.0606 | 0.4475 | 0.0147 | 0.9283 | 0.1139 | 0.016 | 0.055 |
| Male | 4900 | 0.3053 | 0.241 | 0.5809 | 0.0917 | 0.8084 | 0.2983 | 0.0173 | 0.1293 |

| gap | estimate | ci95_low | ci95_high | flagged |
|---|---|---|---|---|
| dp_diff | 0.1805 | 0.1657 | 0.1935 | True |
| eo_diff | 0.1334 | 0.0782 | 0.1887 | True |
| tpr_gap | 0.1334 | 0.077 | 0.1887 | True |
| fpr_gap | 0.0769 | 0.0669 | 0.0885 | True |
| ece_gap | 0.0012 | 0.0002 | 0.013 | False |

A gap is *flagged* when the lower end of its 95% bootstrap interval exceeds 0.05 (a pre-stated tolerance, not a legal standard).

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.901 (n=7327); mean set size 1.122
- Coverage by class: {"0": 0.969, "1": 0.683}
- Coverage by group: {"Female": 0.947, "Male": 0.878}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.

Measured explanation quality for this model (from the experiment result files):

`e1`

| baseline | method | n | deletion_gain_mean | deletion_gain_ci_low | deletion_gain_ci_high | insertion_gain_mean | combined_gain_mean |
|---|---|---|---|---|---|---|---|
| mean | shap | 200 | 0.1707 | 0.1565 | 0.1849 | 0.1275 | 0.1491 |
| mean | lime | 200 | 0.1562 | 0.1422 | 0.1707 | 0.1207 | 0.1385 |
| mean | lpi | 200 | 0.1637 | 0.15 | 0.1778 | 0.126 | 0.1449 |
| mean | global_pi | 200 | 0.0484 | 0.0339 | 0.0636 | 0.0432 | 0.0458 |
| marginal | shap | 200 | 0.1578 | 0.1482 | 0.1677 | 0.1356 | 0.1467 |
| marginal | lime | 200 | 0.1396 | 0.1299 | 0.1498 | 0.122 | 0.1308 |
| marginal | lpi | 200 | 0.1555 | 0.1462 | 0.1651 | 0.1286 | 0.142 |
| marginal | global_pi | 200 | 0.0638 | 0.0529 | 0.0749 | 0.0711 | 0.0675 |

`e2`

| setting | method | n | spearman_mean | spearman_ci_low | spearman_ci_high | topk_jaccard_mean |
|---|---|---|---|---|---|---|
| refit | shap | 50 | 0.9513 | 0.9444 | 0.9582 | 0.8843 |
| refit | lime | 50 | 0.9701 | 0.9674 | 0.973 | 0.848 |
| refit | lpi | 50 | 0.9 | 0.8867 | 0.9127 | 0.8033 |
| explainer_only | shap | 50 | 0.9426 | 0.9339 | 0.951 | 0.8655 |
| explainer_only | lime | 50 | 0.9725 | 0.9698 | 0.9754 | 0.8569 |
| explainer_only | lpi | 50 | 0.9009 | 0.8873 | 0.9145 | 0.8208 |

`e3`

| pair | n | mean_abs_feature_correlation | overlap_mean | overlap_ci_low | overlap_ci_high | chance_overlap |
|---|---|---|---|---|---|---|
| shap-lime | 100 | 0.1086 | 0.714 | 0.694 | 0.734 | 0.5556 |
| shap-lpi | 100 | 0.1086 | 0.916 | 0.896 | 0.936 | 0.5556 |
| lime-lpi | 100 | 0.1086 | 0.732 | 0.708 | 0.754 | 0.5556 |

## Limitations

- Collected in 1994; reflects historical labour-market inequality, so subgroup gaps are largely a property of the data, not only of the model.
- Sex is recorded as a binary attribute; no other identities are represented.
- Proxies for sex (for example marital status and hours per week) remain in the features.
- Not a decision-ready dataset: a benchmark for demonstration only.
- Metrics are from a single train/test split; no confidence intervals are given for accuracy.
- Conformal guarantees are marginal and assume exchangeable data; they say nothing about drift.

## Ethical considerations

- Subgroup gaps measured here describe this dataset and this model, not causes.
- The sensitive attribute is used for auditing only and is not a model input; proxies may remain.
- Passing an audit does not make a model fair; failing one is a prompt to investigate.
