# Model card: adult / mlp

*Small neural network, 32-16 hidden units (black box).* Generated automatically by xaidash v0.1.0.

## Model details

- Interpretable by design: no (black box)
- Hyperparameters: `{"alpha": 0.001, "hidden_layer_sizes": [32, 16], "learning_rate": "constant", "max_iter": 500, "random_state": 611}`
- Library versions: scikit-learn 1.9.1, lightgbm 4.7.0
- Calibration: Platt scaling on the calibration split (slope 0.867, intercept -0.0274)

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
| 7327 | 0.8575 | 0.9108 | 0.3072 | 0.0986 | 0.0093 | 0.015 | 0.1827 | 0.2393 |

## Fairness and calibration by subgroup

| group | n | base_rate | selection_rate | tpr | fpr | accuracy | mean_predicted | ece | brier |
|---|---|---|---|---|---|---|---|---|---|
| Female | 2427 | 0.1059 | 0.061 | 0.4591 | 0.0138 | 0.9304 | 0.1118 | 0.0095 | 0.0535 |
| Male | 4900 | 0.3053 | 0.2431 | 0.6056 | 0.0837 | 0.8214 | 0.3009 | 0.0122 | 0.121 |

| gap | estimate | ci95_low | ci95_high | flagged |
|---|---|---|---|---|
| dp_diff | 0.1821 | 0.1667 | 0.1972 | True |
| eo_diff | 0.1465 | 0.0898 | 0.2115 | True |
| tpr_gap | 0.1465 | 0.0898 | 0.2115 | True |
| fpr_gap | 0.0699 | 0.0606 | 0.0799 | True |
| ece_gap | 0.0027 | 0.0001 | 0.0145 | False |

A gap is *flagged* when the lower end of its 95% bootstrap interval exceeds 0.05 (a pre-stated tolerance, not a legal standard).

## Uncertainty (split conformal prediction sets)

- Target coverage 90%; empirical test coverage 0.899 (n=7327); mean set size 1.099
- Coverage by class: {"0": 0.967, "1": 0.681}
- Coverage by group: {"Female": 0.944, "Male": 0.876}

## Explanations

- Local: SHAP (interventional), LIME (tabular), local permutation; global: permutation importance, partial dependence / ICE
- All attributions explain the raw model log-odds of the positive class.
- Explanation-quality measurements: not available (experiments not run for this model).

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
