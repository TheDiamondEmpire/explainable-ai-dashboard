# Model card: adult / lgbm

*LightGBM gradient boosting (black box).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: no (black box)
- Hyperparameters: `{"colsample_bytree": 0.8, "learning_rate": 0.05, "min_child_samples": 20, "n_estimators": 200, "num_leaves": 15, "random_state": 611, "subsample": 0.8}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 1.041, intercept 0.0434)

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
| 7327 | 0.8691 | 0.9239 | 0.2827 | 0.0906 | 0.0107 | 0.0087 | 0.187 | 0.2393 |

## Fairness and calibration by subgroup

| group | n | base_rate | selection_rate | tpr | fpr | accuracy | mean_predicted | ece | brier |
|---|---|---|---|---|---|---|---|---|---|
| Female | 2427 | 0.1059 | 0.0667 | 0.5136 | 0.0138 | 0.9361 | 0.1073 | 0.0074 | 0.0488 |
| Male | 4900 | 0.3053 | 0.2465 | 0.635 | 0.0758 | 0.8359 | 0.3015 | 0.0158 | 0.1114 |

| gap | estimate | ci95_low | ci95_high | flagged |
|---|---|---|---|---|
| dp_diff | 0.1798 | 0.1642 | 0.1928 | True |
| eo_diff | 0.1214 | 0.065 | 0.1747 | True |
| tpr_gap | 0.1214 | 0.0569 | 0.1747 | True |
| fpr_gap | 0.062 | 0.0536 | 0.071 | True |
| ece_gap | 0.0084 | 0.0002 | 0.0158 | False |

A gap is *flagged* when the lower end of its 95% bootstrap interval exceeds 0.05 (a pre-stated tolerance, not a legal standard).

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.900 (n=7327); mean set size 1.073
- Coverage by class: {"0": 0.965, "1": 0.695}
- Coverage by group: {"Female": 0.947, "Male": 0.877}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.

Measured explanation quality for this model (from the experiment result files):

`e1`

| baseline | method | n | deletion_gain_mean | deletion_gain_ci_low | deletion_gain_ci_high | insertion_gain_mean | combined_gain_mean |
|---|---|---|---|---|---|---|---|
| mean | shap | 200 | 0.1192 | 0.105 | 0.1337 | 0.0794 | 0.0993 |
| mean | lime | 200 | 0.0086 | -0.0061 | 0.0242 | 0.0176 | 0.0131 |
| mean | lpi | 200 | 0.078 | 0.0634 | 0.0928 | 0.0716 | 0.0748 |
| mean | global_pi | 200 | 0.0136 | 0.0026 | 0.0241 | 0.0451 | 0.0293 |
| marginal | shap | 200 | 0.1592 | 0.1504 | 0.1686 | 0.1258 | 0.1425 |
| marginal | lime | 200 | 0.1256 | 0.1158 | 0.1354 | 0.0997 | 0.1126 |
| marginal | lpi | 200 | 0.1421 | 0.1331 | 0.1517 | 0.1195 | 0.1308 |
| marginal | global_pi | 200 | 0.0616 | 0.0523 | 0.0701 | 0.059 | 0.0603 |

`e2`

| setting | method | n | spearman_mean | spearman_ci_low | spearman_ci_high | topk_jaccard_mean |
|---|---|---|---|---|---|---|
| refit | shap | 50 | 0.9154 | 0.9055 | 0.9244 | 0.8066 |
| refit | lime | 50 | 0.9518 | 0.944 | 0.9583 | 0.9256 |
| refit | lpi | 50 | 0.826 | 0.8063 | 0.8446 | 0.7271 |
| explainer_only | shap | 50 | 0.9429 | 0.936 | 0.9491 | 0.8274 |
| explainer_only | lime | 50 | 0.9675 | 0.9635 | 0.9708 | 0.9661 |
| explainer_only | lpi | 50 | 0.8716 | 0.8558 | 0.8853 | 0.7647 |

`e3`

| pair | n | mean_abs_feature_correlation | overlap_mean | overlap_ci_low | overlap_ci_high | chance_overlap |
|---|---|---|---|---|---|---|
| shap-lime | 100 | 0.108 | 0.83 | 0.808 | 0.85 | 0.5556 |
| shap-lpi | 100 | 0.108 | 0.86 | 0.836 | 0.882 | 0.5556 |
| lime-lpi | 100 | 0.108 | 0.786 | 0.76 | 0.814 | 0.5556 |

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
