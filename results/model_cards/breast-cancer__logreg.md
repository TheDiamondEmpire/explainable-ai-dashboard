# Model card: breast-cancer / logreg

*Logistic regression (interpretable).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: yes
- Hyperparameters: `{"C": 1.0, "max_iter": 2000, "random_state": null}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 1.3715, intercept 0.8822)

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
| 86 | 0.9767 | 1 | 0.0457 | 0.0128 | 0.0344 | 0.0404 | 0.3953 | 0.3721 |

## Fairness and calibration by subgroup

Not applicable: this dataset has no sensitive attribute.

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.953 (n=86); mean set size 0.953
- Coverage by class: {"0": 0.926, "1": 1.0}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.

Measured explanation quality for this model (from the experiment result files):

`e1`

| baseline | method | n | deletion_gain_mean | deletion_gain_ci_low | deletion_gain_ci_high | insertion_gain_mean | combined_gain_mean |
|---|---|---|---|---|---|---|---|
| mean | shap | 200 | 0.4435 | 0.4289 | 0.4583 | 0.121 | 0.2823 |
| mean | lime | 200 | 0.3889 | 0.3721 | 0.4054 | 0.1147 | 0.2518 |
| mean | lpi | 200 | 0.439 | 0.4242 | 0.454 | 0.1206 | 0.2798 |
| mean | global_pi | 200 | 0.0372 | 0.0228 | 0.0529 | 0.0261 | 0.0316 |
| marginal | shap | 200 | 0.2469 | 0.2373 | 0.2573 | 0.1526 | 0.1997 |
| marginal | lime | 200 | 0.2209 | 0.211 | 0.2316 | 0.1408 | 0.1808 |
| marginal | lpi | 200 | 0.2388 | 0.2291 | 0.2497 | 0.1528 | 0.1958 |
| marginal | global_pi | 200 | 0.0598 | 0.0511 | 0.0689 | 0.0501 | 0.0549 |

`e2`

| setting | method | n | spearman_mean | spearman_ci_low | spearman_ci_high | topk_jaccard_mean |
|---|---|---|---|---|---|---|
| refit | shap | 50 | 0.8782 | 0.8677 | 0.8881 | 0.5483 |
| refit | lime | 50 | 0.7093 | 0.6733 | 0.7434 | 0.409 |
| refit | lpi | 50 | 0.8362 | 0.8171 | 0.854 | 0.503 |
| explainer_only | shap | 50 | 0.9805 | 0.9756 | 0.9849 | 0.8407 |
| explainer_only | lime | 50 | 0.8372 | 0.8046 | 0.8673 | 0.5771 |
| explainer_only | lpi | 50 | 0.9428 | 0.9294 | 0.9548 | 0.7321 |

`e3`

| pair | n | mean_abs_feature_correlation | overlap_mean | overlap_ci_low | overlap_ci_high | chance_overlap |
|---|---|---|---|---|---|---|
| shap-lime | 100 | 0.3955 | 0.65 | 0.612 | 0.686 | 0.1667 |
| shap-lpi | 100 | 0.3955 | 0.806 | 0.77 | 0.84 | 0.1667 |
| lime-lpi | 100 | 0.3955 | 0.582 | 0.54 | 0.624 | 0.1667 |

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
