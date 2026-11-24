# Model card: breast-cancer / mlp

*Small neural network, 32-16 hidden units (black box).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: no (black box)
- Hyperparameters: `{"alpha": 0.001, "hidden_layer_sizes": [32, 16], "learning_rate": "constant", "max_iter": 500, "random_state": 611}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 0.8936, intercept 0.7326)

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
| 86 | 1 | 1 | 0.0283 | 0.0059 | 0.0245 | 0.018 | 0.3721 | 0.3721 |

## Fairness and calibration by subgroup

Not applicable: this dataset has no sensitive attribute.

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.895 (n=86); mean set size 0.895
- Coverage by class: {"0": 0.87, "1": 0.938}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.
- Explanation-quality measurements: not available (experiments not run for this model).

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
