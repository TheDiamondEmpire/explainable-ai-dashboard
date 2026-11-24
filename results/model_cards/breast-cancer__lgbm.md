# Model card: breast-cancer / lgbm

*LightGBM gradient boosting (black box).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: no (black box)
- Hyperparameters: `{"colsample_bytree": 0.8, "learning_rate": 0.05, "min_child_samples": 20, "n_estimators": 200, "num_leaves": 15, "random_state": 611, "subsample": 0.8}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 0.8527, intercept 0.9102)

## Intended use

- Primary: Demonstration and evaluation of explanation, fairness and uncertainty tooling on a public benchmark.
- Out of scope: Any real decision about people. These models are not validated for deployment.

## Training data

- Dataset: **Wisconsin Diagnostic Breast Cancer** (real public data)
- Source: scikit-learn bundled copy of the UCI WDBC dataset (sklearn.datasets.load_breast_cancer)
- Version: scikit-learn bundled copy; see sha256
- Licence: CC BY 4.0 (UCI Machine Learning Repository)
- Rows: 569, features: 30
- Content hash (sha256): `e1549ecb09c8c0131fcb80d74eefab065f285269d4892e1c551909ad8150c6e3`
- Target: diagnosis (positive = malignant)
- Sensitive attribute: none (not applicable: no sensitive attribute is recorded)

Splits (stratified on the label; one fixed seed): train 312, calibration 85, conformal 86, test 86.

## Performance (held-out test split)

| n | accuracy | auc | log_loss | brier | ece | ece_raw | positive_rate | base_rate |
|---|---|---|---|---|---|---|---|---|
| 86 | 0.9651 | 0.9971 | 0.067 | 0.0219 | 0.0285 | 0.0401 | 0.3837 | 0.3721 |

## Fairness and calibration by subgroup

Not applicable: this dataset has no sensitive attribute.

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.895 (n=86); mean set size 0.895
- Coverage by class: {"0": 0.926, "1": 0.844}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.

Measured explanation quality for this model (from the experiment result files):

`e1`

| baseline | method | n | deletion_gain_mean | deletion_gain_ci_low | deletion_gain_ci_high | insertion_gain_mean | combined_gain_mean |
|---|---|---|---|---|---|---|---|
| mean | shap | 200 | 0.4665 | 0.4405 | 0.4905 | 0.1429 | 0.3047 |
| mean | lime | 200 | 0.3333 | 0.2988 | 0.3666 | 0.1339 | 0.2336 |
| mean | lpi | 200 | 0.438 | 0.4122 | 0.4623 | 0.1375 | 0.2877 |
| mean | global_pi | 200 | 0.1202 | 0.1012 | 0.1393 | 0.0863 | 0.1032 |
| marginal | shap | 200 | 0.217 | 0.2103 | 0.2237 | 0.1791 | 0.1981 |
| marginal | lime | 200 | 0.1957 | 0.1884 | 0.2034 | 0.1613 | 0.1785 |
| marginal | lpi | 200 | 0.2139 | 0.2075 | 0.2204 | 0.1793 | 0.1966 |
| marginal | global_pi | 200 | 0.1385 | 0.1299 | 0.147 | 0.1426 | 0.1406 |

`e2`

| setting | method | n | spearman_mean | spearman_ci_low | spearman_ci_high | topk_jaccard_mean |
|---|---|---|---|---|---|---|
| refit | shap | 50 | 0.7313 | 0.7084 | 0.753 | 0.5837 |
| refit | lime | 50 | 0.514 | 0.4837 | 0.5452 | 0.475 |
| refit | lpi | 50 | 0.6756 | 0.6577 | 0.6925 | 0.5543 |
| explainer_only | shap | 50 | 0.9836 | 0.9786 | 0.9878 | 0.9248 |
| explainer_only | lime | 50 | 0.7195 | 0.6942 | 0.7449 | 0.6894 |
| explainer_only | lpi | 50 | 0.9653 | 0.9594 | 0.9707 | 0.84 |

`e3`

| pair | n | mean_abs_feature_correlation | overlap_mean | overlap_ci_low | overlap_ci_high | chance_overlap |
|---|---|---|---|---|---|---|
| shap-lime | 100 | 0.3864 | 0.788 | 0.75 | 0.822 | 0.1667 |
| shap-lpi | 100 | 0.3864 | 0.828 | 0.802 | 0.854 | 0.1667 |
| lime-lpi | 100 | 0.3864 | 0.718 | 0.68 | 0.754 | 0.1667 |

## Limitations

- 569 rows only; test sets are small and intervals are wide.
- Features are strongly collinear, which stresses attribution methods.
- A teaching benchmark, not a clinical tool.
- Metrics are from a single train/test split; no confidence intervals are given for accuracy.
- Conformal guarantees are marginal and assume exchangeable data; they say nothing about drift.

## Ethical considerations

- Subgroup gaps measured here describe this dataset and this model, not causes.
- The sensitive attribute is used for auditing only and is not a model input; proxies may remain.
- Passing an audit does not make a model fair; failing one is a prompt to investigate.
