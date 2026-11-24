# Explainable AI Prediction Dashboard: Research Report

> **Where the code is.** The experiment scripts that produced these results are part of the source package, which is distributed separately (see [SOURCE-ACCESS.md](../SOURCE-ACCESS.md)). The raw results they wrote are included in this repository under [`results/`](../results/).

<p align="center"><img src="assets/banner.svg" alt="Explainable AI Prediction Dashboard" width="100%"></p>

> **Status: protocol v0.2, results recorded for E1 to E4; E5 (user study) not run.** Hypotheses H1 to H3 were tested on public benchmark data and on synthetic data; two of the three were not supported as originally worded (see the Discussion). H4 (effect of uncertainty displays on human trust calibration) is **untested**: no human participants were available and none were simulated. Every number below was produced by a script in this repository and is stored in `results/`; the tables in this document are rendered from those files.

## Abstract

This study asks how faithful, stable and mutually consistent common post-hoc explanation methods are for tabular models, and whether a fairness and calibration audit can be validated against known ground truth. The system under test is a working dashboard (logistic regression, LightGBM and a small neural network; SHAP, LIME and local permutation explanations; subgroup audit; split-conformal sets; counterfactual search). On three public or bundled datasets and two synthetic generators we found: (E1) all three local methods are far more faithful than a random ordering, and SHAP is slightly but consistently more faithful than local permutation, a difference that is statistically significant but small; (E2) SHAP was **not** more stable than LIME for the tree model on two of three datasets (H1 not supported as a general claim); (E3) SHAP and LIME agree more than expected (top-5 overlap 0.65 to 0.90) and agreement did **not** fall with feature correlation in our synthetic strata (H2 not supported); (E4) the audit's estimates track the planted ground truth, but the pre-stated 0.05 screening rule missed most of the bias that a group-blind model inherits. The human study (E5) was not run, so H4 is untested.

## 1. Research question

How faithful, stable and mutually consistent are common post-hoc explanation methods, and do explanations plus uncertainty displays improve human decision quality and trust calibration?

The second half of the question concerns human users and is **not answered** by this report. The measurable first half is answered for the methods, models and datasets below only.

## 2. Hypotheses

- H1: SHAP attributions are more stable across bootstrap resamples than LIME attributions for tree-based models.
- H2: Agreement between SHAP and LIME (top-5 feature overlap) is moderate at best and falls on correlated features.
- H3: Faithfulness (deletion curves) of SHAP exceeds permutation-importance-derived local explanations.
- H4: Showing conformal uncertainty alongside explanations improves participants’ calibrated trust versus explanations alone. **Untested (E5 not run).**

Operational definitions added in protocol v0.2, fixed before the full experiment run (see the change log):

- H1 is tested on the LightGBM model, "refit" setting, with a one-sided paired Wilcoxon test on per-instance Spearman stability (SHAP minus LIME), Holm-adjusted over the three datasets.
- H2 has two parts. H2a ("moderate at best") is met in a cell when the upper end of the 95% CI of the SHAP-LIME top-5 overlap is at most 0.70. H2b (falls with correlation) is a one-sided Spearman trend test of the correlation level against per-instance overlap over the four synthetic strata, per model, Holm-adjusted over the two models.
- H3 uses the deletion gain (random-control deletion AUC minus method deletion AUC; positive is better) with the marginal baseline as primary and the mean baseline as sensitivity analysis. A significant difference smaller than 0.01 in deletion gain (about 7% of a typical gain of 0.15) is described as small; this size threshold is descriptive and was chosen after seeing the data.

## 3. Background

Post-hoc explanation methods such as SHAP (Lundberg and Lee, 2017) and LIME (Ribeiro et al., 2016) are widely used to justify model outputs, but they are approximations and can disagree (Krishna et al., 2022) or be unstable (Alvarez-Melis and Jaakkola, 2018). Faithfulness is commonly assessed by removing or restoring features in order of attributed importance and tracking the model output (deletion and insertion curves; Petsiuk et al., 2018). These perturbation tests have their own pitfalls, because removed features create out-of-distribution inputs and results depend on the baseline value (Hooker et al., 2019), which is why this study reports two baselines. Fairness audits compare error and selection rates across groups (Hardt et al., 2016), and model cards standardise reporting (Mitchell et al., 2019). Split-conformal prediction gives distribution-free coverage guarantees under exchangeability (Angelopoulos and Bates, 2021), and counterfactual explanations describe the smallest change that would alter a decision (Wachter et al., 2017). This study combines these pieces in one tested tool and measures the explanation methods themselves.

## 4. Method

### 4.1 System under test

A dashboard that puts a model’s reasoning, fairness profile and uncertainty next to every prediction, as built in this repository:

- Models: logistic regression (interpretable), LightGBM (200 trees, 15 leaves) and a 32-16 neural network (both black boxes), with Platt-scaled probabilities fitted on a separate calibration split.
- Local explanations (all explain the raw model log-odds): SHAP (closed form for logistic regression, interventional TreeExplainer for LightGBM, permutation explainer for the network), LIME (tabular, quartile discretisation, 1000 samples in experiments) and local permutation (LPI: output minus its mean when one feature is replaced by background values, 30 background rows).
- Global explanations: permutation importance (log-loss increase) and partial dependence / ICE.
- Audit: demographic parity difference, equalized odds difference (and its TPR and FPR parts) and per-group expected calibration error (10 equal-width bins), with 95% percentile bootstrap intervals. A gap is "flagged" when the lower end of its interval exceeds a pre-stated screening tolerance of 0.05 (a project convention, not a legal standard).
- Uncertainty: split-conformal prediction sets (least-ambiguous-set score), marginal or class-conditional, plus regression intervals in the library.
- Counterfactuals: a tested greedy search with pruning that respects immutable features, monotone directions, integer features and observed ranges; cost is L1 distance in median-absolute-deviation units.
- Data splits: stratified 55% train, 15% calibration, 15% conformal, 15% test.

### 4.2 Environment

Datasets (every dataset is labelled with source, version and a content hash in `results/data_manifest.json`):

| Dataset | Source and version | Size | Role |
|---|---|---|---|
| adult | OpenML `adult`, version 2 (data id 1590), originally UCI Machine Learning Repository (1994 US census extract); 9 of 14 columns used, categoricals reduced to binary indicators; sensitive attribute: sex (audit only) | 48,842 rows, 9 features | real, historically biased; audit and explanation experiments |
| breast-cancer | scikit-learn bundled copy of the UCI Wisconsin Diagnostic Breast Cancer data; no sensitive attribute | 569 rows, 30 features | real, highly correlated features |
| synthetic-lending | **SYNTHETIC**: generator v1 in `xaidash.data`, n=20000, seed 7, planted group bias strength 1 | 20,000 rows, 7 features | explanation experiments; audit validation |
| synthetic-corr-0, -0.3, -0.6, -0.9 | **SYNTHETIC**: ten equicorrelated Gaussian features, pairwise correlation 0, 0.3, 0.6, 0.9; five informative | 6,000 rows each, 10 features | E3 correlation strata |
| diabetes | scikit-learn bundled copy of the diabetes regression data | 442 rows | conformal regression check only |

Three model families (logistic regression, LightGBM, small neural network); the neural network is used only in E4 and the supporting checks, because exact explanation experiments on it are slow.

Sample sizes and seeds. All seeds derive from one master seed (20240611) through SHA-256 of a descriptive label. E1: 200 explained instances per dataset-model cell, 20 random orders per instance as control, 20 background rows for the marginal baseline. E2: 30 bootstrap resamples per cell, 50 explained instances. E3: 100 instances per cell. E4 part A: 10 random splits per dataset, 200 bootstrap resamples per audit. E4 part B: 30 replicates per planted-bias strength (n=12,000, test n=1,800), ground truth from a fresh 200,000-row population sample. Supporting checks: 30 random splits (conformal), 100 (regression), 50 counterfactual instances per cell.

Hardware and load. The full run (all experiments, 4,035 s of wall-clock time in one invocation) used at most 2 BLAS/OpenMP threads and `n_jobs=2` on a machine with 8 logical (4 physical) cores reported by the OS as "AMD64 Family 25 Model 1", Windows 11, Python 3.12.13, numpy 2.5.4, pandas 3.1.0, scikit-learn 1.9.1, LightGBM 4.7.0, SHAP 0.54.1. The machine was shared and heavily loaded: CPU load was 100% at start and about 2.9 of 28 GB of memory was available (`results/run_manifest.json`). Wall-clock times are therefore informational only and no conclusion depends on timing.

### 4.3 Baselines

- Random-attribution control: the mean of 20 random feature orders per instance (E1)
- Global permutation importance used as a fixed ordering for every instance ("global_pi", E1)
- Removal baselines for the deletion and insertion curves: training mean, and 20 background rows averaged ("marginal")
- Oracle predictor (the exact outcome probability) as the reference for the audit validation (E4)

### 4.4 Independent variables

- Model family (logistic regression, LightGBM, neural network)
- Explanation method (SHAP, LIME, local permutation)
- Feature correlation level (synthetic strata and measured mean absolute correlation of the real datasets)
- Planted bias strength (0 or 1)
- Uncertainty display on/off (E5 only; not run)

### 4.5 Metrics

- Deletion and insertion AUC (faithfulness), summarised as gain over the random control
- Mean pairwise Spearman rank correlation of signed attribution vectors across replicates (stability), and top-5 Jaccard
- Top-5 feature overlap between methods, with the chance level 5/d
- Subgroup calibration error (ECE) gap, demographic-parity gap and equalized-odds gap
- Conformal coverage and set size; counterfactual validity, sparsity and cost
- User study: decision accuracy, reliance calibration, time (**not measured**)

## 5. Experiment matrix

| ID | Experiment | Manipulation | Measured | Status |
|---|---|---|---|---|
| E1 | Faithfulness | Deletion/insertion curves for each method vs. random control | AUC difference vs. control | run |
| E2 | Stability | 30 bootstrap resamples per model | Mean Spearman ρ of attributions | run |
| E3 | Method agreement | Compare SHAP, LIME and permutation importance | Top-5 overlap, by correlation strata | run |
| E4 | Fairness and calibration | Subgroup audit across models, validated on planted bias | ECE gap, equalized-odds gap | run |
| E5 | User study | Explanation vs. explanation + uncertainty (small N) | Decision accuracy, trust calibration | **not run** |
| S1, S2 | Supporting checks (not pre-specified hypotheses) | Conformal coverage; counterfactual validity | Coverage, validity | run |

## 6. Statistical plan

- **Replication:** run counts are fixed in the experiment scripts and reported above.
- **Summaries:** means with percentile bootstrap 95% confidence intervals (2,000 resamples). Intervals for per-instance metrics resample instances; they reflect instance sampling but not all sources of variation (see Threats to validity).
- **Comparisons:** paired one-sided Wilcoxon signed-rank tests on per-instance differences.
- **Effect sizes:** reported alongside p-values as the paired dominance statistic P(d>0) − P(d<0) (a paired Cliff’s delta); with 50 to 400 instances tiny p-values are common, so effect sizes carry the interpretation, and a significant but trivial effect is called trivial.
- **Multiple comparisons:** Holm-Bonferroni within each pre-stated family (H1: three datasets; H3: six dataset-model cells per baseline; H2b: two models; "better than random": per baseline).
- **No silent exclusions:** no instance, replicate or cell was excluded.
- **Negative results are results:** unsupported hypotheses are reported as unsupported.

## 7. Results

All tables are rendered from `results/` by a script (see Verification). Raw per-instance and per-replicate rows are in the `*_raw.csv` files.

### E1. Faithfulness

**Manipulation:** Deletion/insertion curves for each method vs. random control
**Measured:** AUC difference vs. control (deletion gain; positive means the method deletes faster than random)

| Condition (dataset / model / method) | n | Deletion gain, marginal baseline | 95% CI | Notes |
|---|---|---|---|---|
| synthetic-lending / logreg / shap | 200 | 0.157 | 0.150 to 0.166 | insertion gain 0.135; deletion gain with mean baseline 0.174; 100% of instances above random |
| synthetic-lending / logreg / lime | 200 | 0.153 | 0.146 to 0.161 | insertion gain 0.131; deletion gain with mean baseline 0.168; 100% of instances above random |
| synthetic-lending / logreg / lpi | 200 | 0.153 | 0.145 to 0.161 | insertion gain 0.132; deletion gain with mean baseline 0.171; 100% of instances above random |
| synthetic-lending / logreg / global_pi | 200 | 0.046 | 0.033 to 0.058 | insertion gain 0.045; deletion gain with mean baseline 0.051; 70% of instances above random |
| synthetic-lending / lgbm / shap | 200 | 0.152 | 0.145 to 0.159 | insertion gain 0.127; deletion gain with mean baseline 0.192; 100% of instances above random |
| synthetic-lending / lgbm / lime | 200 | 0.142 | 0.135 to 0.149 | insertion gain 0.119; deletion gain with mean baseline 0.182; 100% of instances above random |
| synthetic-lending / lgbm / lpi | 200 | 0.149 | 0.142 to 0.156 | insertion gain 0.125; deletion gain with mean baseline 0.189; 100% of instances above random |
| synthetic-lending / lgbm / global_pi | 200 | 0.058 | 0.049 to 0.067 | insertion gain 0.058; deletion gain with mean baseline 0.049; 81% of instances above random |
| adult / logreg / shap | 200 | 0.158 | 0.148 to 0.168 | insertion gain 0.136; deletion gain with mean baseline 0.171; 100% of instances above random |
| adult / logreg / lime | 200 | 0.140 | 0.130 to 0.150 | insertion gain 0.122; deletion gain with mean baseline 0.156; 100% of instances above random |
| adult / logreg / lpi | 200 | 0.156 | 0.146 to 0.165 | insertion gain 0.129; deletion gain with mean baseline 0.164; 100% of instances above random |
| adult / logreg / global_pi | 200 | 0.064 | 0.053 to 0.075 | insertion gain 0.071; deletion gain with mean baseline 0.048; 76% of instances above random |
| adult / lgbm / shap | 200 | 0.159 | 0.150 to 0.169 | insertion gain 0.126; deletion gain with mean baseline 0.119; 100% of instances above random |
| adult / lgbm / lime | 200 | 0.126 | 0.116 to 0.135 | insertion gain 0.100; deletion gain with mean baseline 0.009; 98% of instances above random |
| adult / lgbm / lpi | 200 | 0.142 | 0.133 to 0.152 | insertion gain 0.120; deletion gain with mean baseline 0.078; 100% of instances above random |
| adult / lgbm / global_pi | 200 | 0.062 | 0.052 to 0.070 | insertion gain 0.059; deletion gain with mean baseline 0.014; 80% of instances above random |
| breast-cancer / logreg / shap | 200 | 0.247 | 0.237 to 0.257 | insertion gain 0.153; deletion gain with mean baseline 0.444; 100% of instances above random |
| breast-cancer / logreg / lime | 200 | 0.221 | 0.211 to 0.232 | insertion gain 0.141; deletion gain with mean baseline 0.389; 100% of instances above random |
| breast-cancer / logreg / lpi | 200 | 0.239 | 0.229 to 0.250 | insertion gain 0.153; deletion gain with mean baseline 0.439; 100% of instances above random |
| breast-cancer / logreg / global_pi | 200 | 0.060 | 0.051 to 0.069 | insertion gain 0.050; deletion gain with mean baseline 0.037; 88% of instances above random |
| breast-cancer / lgbm / shap | 200 | 0.217 | 0.210 to 0.224 | insertion gain 0.179; deletion gain with mean baseline 0.467; 100% of instances above random |
| breast-cancer / lgbm / lime | 200 | 0.196 | 0.188 to 0.203 | insertion gain 0.161; deletion gain with mean baseline 0.333; 100% of instances above random |
| breast-cancer / lgbm / lpi | 200 | 0.214 | 0.208 to 0.220 | insertion gain 0.179; deletion gain with mean baseline 0.438; 100% of instances above random |
| breast-cancer / lgbm / global_pi | 200 | 0.139 | 0.130 to 0.147 | insertion gain 0.143; deletion gain with mean baseline 0.120; 98% of instances above random |

H3 paired comparison (SHAP minus local permutation, deletion gain per instance):

| Condition (dataset / model / baseline) | n | Mean difference | Dominance (P(d>0) - P(d<0)) | Holm-adjusted one-sided p |
|---|---|---|---|---|
| synthetic-lending / logreg / mean | 200 | 0.0037 | 0.55 | 1.7e-21 |
| synthetic-lending / logreg / marginal | 200 | 0.0049 | 0.38 | 2.8e-15 |
| synthetic-lending / lgbm / mean | 200 | 0.0025 | 0.09 | 0.0019 |
| synthetic-lending / lgbm / marginal | 200 | 0.0032 | 0.38 | 7.4e-10 |
| adult / logreg / mean | 200 | 0.0069 | 0.74 | 3.2e-25 |
| adult / logreg / marginal | 200 | 0.0023 | 0.14 | 0.034 |
| adult / lgbm / mean | 200 | 0.0412 | 0.69 | 1.1e-27 |
| adult / lgbm / marginal | 200 | 0.0171 | 0.93 | 5e-33 |
| breast-cancer / logreg / mean | 200 | 0.0046 | 0.93 | 2.3e-33 |
| breast-cancer / logreg / marginal | 200 | 0.0080 | 0.77 | 3.6e-28 |
| breast-cancer / lgbm / mean | 200 | 0.0285 | 0.82 | 1.8e-29 |
| breast-cancer / lgbm / marginal | 200 | 0.0031 | 0.35 | 1.1e-08 |

### E2. Stability

**Manipulation:** 30 bootstrap resamples per model ("refit"), or one fixed model with 30 explainer-only repeats ("explainer_only")
**Measured:** Mean pairwise Spearman ρ of attributions across the 30 replicates, per instance

| Condition (dataset / model / setting / method) | n instances | Mean pairwise Spearman | 95% CI | Notes |
|---|---|---|---|---|
| synthetic-lending / logreg / refit / shap | 50 | 0.955 | 0.942 to 0.967 | top-5 Jaccard 0.880 |
| synthetic-lending / logreg / refit / lime | 50 | 0.940 | 0.925 to 0.952 | top-5 Jaccard 0.830 |
| synthetic-lending / logreg / refit / lpi | 50 | 0.919 | 0.899 to 0.936 | top-5 Jaccard 0.821 |
| synthetic-lending / logreg / explainer_only / shap | 50 | 0.963 | 0.951 to 0.972 | top-5 Jaccard 0.886 |
| synthetic-lending / logreg / explainer_only / lime | 50 | 0.949 | 0.937 to 0.960 | top-5 Jaccard 0.839 |
| synthetic-lending / logreg / explainer_only / lpi | 50 | 0.910 | 0.891 to 0.928 | top-5 Jaccard 0.817 |
| synthetic-lending / lgbm / refit / shap | 50 | 0.904 | 0.880 to 0.925 | top-5 Jaccard 0.778 |
| synthetic-lending / lgbm / refit / lime | 50 | 0.926 | 0.910 to 0.939 | top-5 Jaccard 0.802 |
| synthetic-lending / lgbm / refit / lpi | 50 | 0.809 | 0.776 to 0.840 | top-5 Jaccard 0.695 |
| synthetic-lending / lgbm / explainer_only / shap | 50 | 0.956 | 0.943 to 0.968 | top-5 Jaccard 0.879 |
| synthetic-lending / lgbm / explainer_only / lime | 50 | 0.942 | 0.930 to 0.952 | top-5 Jaccard 0.822 |
| synthetic-lending / lgbm / explainer_only / lpi | 50 | 0.903 | 0.877 to 0.927 | top-5 Jaccard 0.815 |
| adult / logreg / refit / shap | 50 | 0.951 | 0.944 to 0.958 | top-5 Jaccard 0.884 |
| adult / logreg / refit / lime | 50 | 0.970 | 0.967 to 0.973 | top-5 Jaccard 0.848 |
| adult / logreg / refit / lpi | 50 | 0.900 | 0.887 to 0.913 | top-5 Jaccard 0.803 |
| adult / logreg / explainer_only / shap | 50 | 0.943 | 0.934 to 0.951 | top-5 Jaccard 0.866 |
| adult / logreg / explainer_only / lime | 50 | 0.973 | 0.970 to 0.975 | top-5 Jaccard 0.857 |
| adult / logreg / explainer_only / lpi | 50 | 0.901 | 0.887 to 0.915 | top-5 Jaccard 0.821 |
| adult / lgbm / refit / shap | 50 | 0.915 | 0.906 to 0.924 | top-5 Jaccard 0.807 |
| adult / lgbm / refit / lime | 50 | 0.952 | 0.944 to 0.958 | top-5 Jaccard 0.926 |
| adult / lgbm / refit / lpi | 50 | 0.826 | 0.806 to 0.845 | top-5 Jaccard 0.727 |
| adult / lgbm / explainer_only / shap | 50 | 0.943 | 0.936 to 0.949 | top-5 Jaccard 0.827 |
| adult / lgbm / explainer_only / lime | 50 | 0.967 | 0.963 to 0.971 | top-5 Jaccard 0.966 |
| adult / lgbm / explainer_only / lpi | 50 | 0.872 | 0.856 to 0.885 | top-5 Jaccard 0.765 |
| breast-cancer / logreg / refit / shap | 50 | 0.878 | 0.868 to 0.888 | top-5 Jaccard 0.548 |
| breast-cancer / logreg / refit / lime | 50 | 0.709 | 0.673 to 0.743 | top-5 Jaccard 0.409 |
| breast-cancer / logreg / refit / lpi | 50 | 0.836 | 0.817 to 0.854 | top-5 Jaccard 0.503 |
| breast-cancer / logreg / explainer_only / shap | 50 | 0.980 | 0.976 to 0.985 | top-5 Jaccard 0.841 |
| breast-cancer / logreg / explainer_only / lime | 50 | 0.837 | 0.805 to 0.867 | top-5 Jaccard 0.577 |
| breast-cancer / logreg / explainer_only / lpi | 50 | 0.943 | 0.929 to 0.955 | top-5 Jaccard 0.732 |
| breast-cancer / lgbm / refit / shap | 50 | 0.731 | 0.708 to 0.753 | top-5 Jaccard 0.584 |
| breast-cancer / lgbm / refit / lime | 50 | 0.514 | 0.484 to 0.545 | top-5 Jaccard 0.475 |
| breast-cancer / lgbm / refit / lpi | 50 | 0.676 | 0.658 to 0.692 | top-5 Jaccard 0.554 |
| breast-cancer / lgbm / explainer_only / shap | 50 | 0.984 | 0.979 to 0.988 | top-5 Jaccard 0.925 |
| breast-cancer / lgbm / explainer_only / lime | 50 | 0.720 | 0.694 to 0.745 | top-5 Jaccard 0.689 |
| breast-cancer / lgbm / explainer_only / lpi | 50 | 0.965 | 0.959 to 0.971 | top-5 Jaccard 0.840 |

SHAP minus LIME stability, paired by instance:

| Condition | n | Mean difference | Dominance | Status |
|---|---|---|---|---|
| synthetic-lending / logreg / refit | 50 | 0.015 | 0.28 | exploratory |
| synthetic-lending / logreg / explainer_only | 50 | 0.014 | 0.32 | exploratory |
| synthetic-lending / lgbm / refit | 50 | -0.022 | -0.28 | confirmatory H1 (Holm p 1) |
| synthetic-lending / lgbm / explainer_only | 50 | 0.015 | 0.40 | exploratory |
| adult / logreg / refit | 50 | -0.019 | -0.56 | exploratory |
| adult / logreg / explainer_only | 50 | -0.030 | -0.72 | exploratory |
| adult / lgbm / refit | 50 | -0.036 | -0.88 | confirmatory H1 (Holm p 1) |
| adult / lgbm / explainer_only | 50 | -0.025 | -0.72 | exploratory |
| breast-cancer / logreg / refit | 50 | 0.169 | 1.00 | exploratory |
| breast-cancer / logreg / explainer_only | 50 | 0.143 | 1.00 | exploratory |
| breast-cancer / lgbm / refit | 50 | 0.217 | 1.00 | confirmatory H1 (Holm p 2.7e-15) |
| breast-cancer / lgbm / explainer_only | 50 | 0.264 | 1.00 | exploratory |

### E3. Method agreement

**Manipulation:** Compare SHAP, LIME and permutation importance
**Measured:** Top-5 overlap, by correlation strata

| Condition (dataset / model), SHAP vs LIME | n instances | Top-5 overlap | 95% CI | Notes |
|---|---|---|---|---|
| synthetic-corr-0 / logreg | 100 | 0.862 | 0.838 to 0.886 | mean abs feature correlation 0.02; chance overlap 0.50; Spearman 0.82 |
| synthetic-corr-0 / lgbm | 100 | 0.788 | 0.760 to 0.816 | mean abs feature correlation 0.02; chance overlap 0.50; Spearman 0.81 |
| synthetic-corr-0.3 / logreg | 100 | 0.870 | 0.844 to 0.896 | mean abs feature correlation 0.31; chance overlap 0.50; Spearman 0.84 |
| synthetic-corr-0.3 / lgbm | 100 | 0.830 | 0.802 to 0.856 | mean abs feature correlation 0.30; chance overlap 0.50; Spearman 0.82 |
| synthetic-corr-0.6 / logreg | 100 | 0.852 | 0.826 to 0.878 | mean abs feature correlation 0.59; chance overlap 0.50; Spearman 0.87 |
| synthetic-corr-0.6 / lgbm | 100 | 0.844 | 0.814 to 0.872 | mean abs feature correlation 0.60; chance overlap 0.50; Spearman 0.72 |
| synthetic-corr-0.9 / logreg | 100 | 0.860 | 0.830 to 0.888 | mean abs feature correlation 0.90; chance overlap 0.50; Spearman 0.86 |
| synthetic-corr-0.9 / lgbm | 100 | 0.754 | 0.722 to 0.786 | mean abs feature correlation 0.90; chance overlap 0.50; Spearman 0.79 |
| synthetic-lending / logreg | 100 | 0.900 | 0.880 to 0.920 | mean abs feature correlation 0.07; chance overlap 0.71; Spearman 0.91 |
| synthetic-lending / lgbm | 100 | 0.846 | 0.824 to 0.868 | mean abs feature correlation 0.07; chance overlap 0.71; Spearman 0.90 |
| adult / logreg | 100 | 0.714 | 0.694 to 0.734 | mean abs feature correlation 0.11; chance overlap 0.56; Spearman 0.85 |
| adult / lgbm | 100 | 0.830 | 0.808 to 0.850 | mean abs feature correlation 0.11; chance overlap 0.56; Spearman 0.84 |
| breast-cancer / logreg | 100 | 0.650 | 0.612 to 0.686 | mean abs feature correlation 0.40; chance overlap 0.17; Spearman 0.83 |
| breast-cancer / lgbm | 100 | 0.788 | 0.750 to 0.822 | mean abs feature correlation 0.39; chance overlap 0.17; Spearman 0.77 |

Other method pairs (mean top-5 overlap):

| Condition | SHAP vs local permutation | LIME vs local permutation |
|---|---|---|
| synthetic-corr-0 / logreg | 0.934 | 0.862 |
| synthetic-corr-0 / lgbm | 0.870 | 0.760 |
| synthetic-corr-0.3 / logreg | 0.948 | 0.874 |
| synthetic-corr-0.3 / lgbm | 0.864 | 0.806 |
| synthetic-corr-0.6 / logreg | 0.960 | 0.854 |
| synthetic-corr-0.6 / lgbm | 0.888 | 0.804 |
| synthetic-corr-0.9 / logreg | 0.978 | 0.862 |
| synthetic-corr-0.9 / lgbm | 0.800 | 0.710 |
| synthetic-lending / logreg | 0.916 | 0.878 |
| synthetic-lending / lgbm | 0.920 | 0.818 |
| adult / logreg | 0.916 | 0.732 |
| adult / lgbm | 0.860 | 0.786 |
| breast-cancer / logreg | 0.806 | 0.582 |
| breast-cancer / lgbm | 0.828 | 0.718 |

H2b trend test over the synthetic correlation strata (one-sided Spearman, rho vs SHAP-LIME overlap):

| Model | n | Spearman statistic | p (one-sided) | Holm p |
|---|---|---|---|---|
| logreg | 400 | -0.005 | 0.458 | 0.458 |
| lgbm | 400 | -0.051 | 0.155 | 0.309 |

Global agreement (Spearman between mean absolute SHAP and permutation importance):

| Condition | n instances | Spearman | 95% CI (resampling instances) | Notes |
|---|---|---|---|---|
| synthetic-corr-0 / logreg | 100 | 0.79 | 0.79 to 0.81 | 10 features |
| synthetic-corr-0 / lgbm | 100 | 0.84 | 0.77 to 0.85 | 10 features |
| synthetic-corr-0.3 / logreg | 100 | 0.88 | 0.88 to 0.89 | 10 features |
| synthetic-corr-0.3 / lgbm | 100 | 0.96 | 0.88 to 0.99 | 10 features |
| synthetic-corr-0.6 / logreg | 100 | 0.90 | 0.85 to 0.90 | 10 features |
| synthetic-corr-0.6 / lgbm | 100 | 0.92 | 0.85 to 0.96 | 10 features |
| synthetic-corr-0.9 / logreg | 100 | 0.99 | 0.98 to 0.99 | 10 features |
| synthetic-corr-0.9 / lgbm | 100 | 0.95 | 0.90 to 1.00 | 10 features |
| synthetic-lending / logreg | 100 | 0.96 | 0.93 to 1.00 | 7 features |
| synthetic-lending / lgbm | 100 | 0.96 | 0.89 to 0.96 | 7 features |
| adult / logreg | 100 | 0.95 | 0.92 to 0.95 | 9 features |
| adult / lgbm | 100 | 0.90 | 0.82 to 0.93 | 9 features |
| breast-cancer / logreg | 100 | 0.84 | 0.80 to 0.85 | 30 features |
| breast-cancer / lgbm | 100 | 0.51 | 0.49 to 0.53 | 30 features |

### E4. Fairness and calibration

**Manipulation:** Subgroup audit across models; planted-bias validation
**Measured:** ECE gap, demographic-parity and equalized-odds gaps

| Condition (dataset / model) | n repeats | DP gap (95% CI over repeats) | EO gap | ECE gap | Notes |
|---|---|---|---|---|---|
| adult / logreg | 10 | 0.182 (0.177 to 0.187) | 0.150 (0.134 to 0.165) | 0.005 (0.003 to 0.008) | accuracy 0.847; AUC 0.898; DP flagged in 100% of repeats |
| adult / lgbm | 10 | 0.186 (0.180 to 0.191) | 0.131 (0.109 to 0.153) | 0.004 (0.002 to 0.007) | accuracy 0.868; AUC 0.923; DP flagged in 100% of repeats |
| adult / mlp | 10 | 0.180 (0.174 to 0.186) | 0.141 (0.125 to 0.155) | 0.003 (0.002 to 0.005) | accuracy 0.853; AUC 0.908; DP flagged in 100% of repeats |
| synthetic-lending / logreg | 10 | 0.050 (0.038 to 0.065) | 0.039 (0.030 to 0.053) | 0.029 (0.020 to 0.039) | accuracy 0.774; AUC 0.843; DP flagged in 0% of repeats |
| synthetic-lending / lgbm | 10 | 0.065 (0.052 to 0.080) | 0.043 (0.027 to 0.060) | 0.025 (0.018 to 0.033) | accuracy 0.771; AUC 0.840; DP flagged in 20% of repeats |
| synthetic-lending / mlp | 10 | 0.061 (0.046 to 0.077) | 0.043 (0.028 to 0.061) | 0.026 (0.018 to 0.034) | accuracy 0.765; AUC 0.834; DP flagged in 20% of repeats |

Validation against the planted bias (population truth from 200000 fresh rows):

| Condition (predictor / strength / gap) | n replicates | Mean estimate | Population value | CI coverage of truth (Wilson 95%) | Flag rate at 0.05 |
|---|---|---|---|---|---|
| logreg / strength 0 / dp_diff | 30 | 0.020 | 0.007 | 0.77 (0.59 to 0.88) | 0.00 |
| logreg / strength 0 / eo_diff | 30 | 0.034 | 0.008 | 0.30 (0.17 to 0.48) | 0.00 |
| logreg / strength 0 / ece_gap | 30 | 0.013 | 0.002 | 0.73 (0.56 to 0.86) | 0.00 |
| lgbm / strength 0 / dp_diff | 30 | 0.019 | 0.006 | 0.80 (0.63 to 0.90) | 0.00 |
| lgbm / strength 0 / eo_diff | 30 | 0.030 | 0.007 | 0.30 (0.17 to 0.48) | 0.00 |
| lgbm / strength 0 / ece_gap | 30 | 0.013 | 0.003 | 0.73 (0.56 to 0.86) | 0.00 |
| mlp / strength 0 / dp_diff | 30 | 0.026 | 0.008 | 0.77 (0.59 to 0.88) | 0.00 |
| mlp / strength 0 / eo_diff | 30 | 0.041 | 0.011 | 0.37 (0.22 to 0.54) | 0.00 |
| mlp / strength 0 / ece_gap | 30 | 0.014 | 0.004 | 0.73 (0.56 to 0.86) | 0.00 |
| oracle / strength 0 / dp_diff | 30 | 0.021 | 0.003 | 0.87 (0.70 to 0.95) | 0.00 |
| oracle / strength 0 / eo_diff | 30 | 0.033 | 0.004 | 0.00 (0.00 to 0.11) | 0.00 |
| oracle / strength 0 / ece_gap | 30 | 0.010 | 0.000 | 0.00 (0.00 to 0.11) | 0.00 |
| logreg / strength 1 / dp_diff | 30 | 0.047 | 0.049 | 0.90 (0.74 to 0.97) | 0.03 |
| logreg / strength 1 / eo_diff | 30 | 0.044 | 0.035 | 0.83 (0.66 to 0.93) | 0.10 |
| logreg / strength 1 / ece_gap | 30 | 0.034 | 0.031 | 0.97 (0.83 to 0.99) | 0.00 |
| lgbm / strength 1 / dp_diff | 30 | 0.060 | 0.064 | 0.97 (0.83 to 0.99) | 0.13 |
| lgbm / strength 1 / eo_diff | 30 | 0.045 | 0.026 | 0.87 (0.70 to 0.95) | 0.03 |
| lgbm / strength 1 / ece_gap | 30 | 0.032 | 0.028 | 0.87 (0.70 to 0.95) | 0.00 |
| mlp / strength 1 / dp_diff | 30 | 0.060 | 0.063 | 0.93 (0.79 to 0.98) | 0.13 |
| mlp / strength 1 / eo_diff | 30 | 0.044 | 0.030 | 0.93 (0.79 to 0.98) | 0.03 |
| mlp / strength 1 / ece_gap | 30 | 0.033 | 0.029 | 0.93 (0.79 to 0.98) | 0.00 |
| oracle / strength 1 / dp_diff | 30 | 0.187 | 0.186 | 0.97 (0.83 to 0.99) | 1.00 |
| oracle / strength 1 / eo_diff | 30 | 0.152 | 0.146 | 1.00 (0.89 to 1.00) | 0.97 |
| oracle / strength 1 / ece_gap | 30 | 0.012 | 0.000 | 0.20 (0.10 to 0.37) | 0.00 |

Pre-stated validation criteria:

| Criterion | Predictor | Strength | Value | Verdict |
|---|---|---|---|---|
| V1 oracle CI coverage >= 0.90 | oracle | 0 | 0.867 | NOT met |
| V1 oracle CI coverage >= 0.90 | oracle | 1 | 0.967 | met |
| V1 oracle CI coverage >= 0.90 | oracle | 0 | 0.000 | NOT met |
| V1 oracle CI coverage >= 0.90 | oracle | 1 | 1.000 | met |
| V2 oracle DP gap flagged in >= 90% at strength 1 | oracle | 1 | 1.000 | met |
| V2b |mean DP estimate - population value| <= 0.02 at strength 1 | logreg | 1 | 0.001 | met |
| V3 DP gap flagged in <= 10% at strength 0 | logreg | 0 | 0.000 | met |
| V4 ECE gap larger at strength 1 than 0 (paired Wilcoxon, one-sided p) | logreg | 1 | 0.000 | met |
| V2b |mean DP estimate - population value| <= 0.02 at strength 1 | lgbm | 1 | 0.004 | met |
| V3 DP gap flagged in <= 10% at strength 0 | lgbm | 0 | 0.000 | met |
| V4 ECE gap larger at strength 1 than 0 (paired Wilcoxon, one-sided p) | lgbm | 1 | 0.000 | met |
| V2b |mean DP estimate - population value| <= 0.02 at strength 1 | mlp | 1 | 0.003 | met |
| V3 DP gap flagged in <= 10% at strength 0 | mlp | 0 | 0.000 | met |
| V4 ECE gap larger at strength 1 than 0 (paired Wilcoxon, one-sided p) | mlp | 1 | 0.000 | met |
| V3 DP gap flagged in <= 10% at strength 0 | oracle | 0 | 0.000 | met |

### Supporting checks (S1 conformal coverage, S2 counterfactual validity)

These were not pre-specified hypotheses; they check that the uncertainty and what-if components behave as designed.

| Condition | n splits | Coverage (target 0.90) | 95% CI | Notes |
|---|---|---|---|---|
| synthetic-lending / logreg / marginal | 30 | 0.901 | 0.899 to 0.904 | class 0 0.94, class 1 0.84; mean set size 1.32; group A 0.90; group B 0.90 |
| synthetic-lending / logreg / class-conditional | 30 | 0.902 | 0.899 to 0.904 | class 0 0.90, class 1 0.90; mean set size 1.34; group A 0.91; group B 0.89 |
| synthetic-lending / lgbm / marginal | 30 | 0.901 | 0.898 to 0.903 | class 0 0.94, class 1 0.84; mean set size 1.32; group A 0.90; group B 0.90 |
| synthetic-lending / lgbm / class-conditional | 30 | 0.903 | 0.901 to 0.905 | class 0 0.90, class 1 0.90; mean set size 1.35; group A 0.91; group B 0.90 |
| adult / logreg / marginal | 30 | 0.899 | 0.898 to 0.901 | class 0 0.97, class 1 0.68; mean set size 1.12; group Female 0.94; group Male 0.88 |
| adult / logreg / class-conditional | 30 | 0.899 | 0.897 to 0.900 | class 0 0.90, class 1 0.90; mean set size 1.19; group Female 0.95; group Male 0.87 |
| adult / lgbm / marginal | 30 | 0.899 | 0.897 to 0.901 | class 0 0.96, class 1 0.70; mean set size 1.07; group Female 0.95; group Male 0.88 |
| adult / lgbm / class-conditional | 30 | 0.899 | 0.897 to 0.901 | class 0 0.90, class 1 0.90; mean set size 1.13; group Female 0.95; group Male 0.87 |
| breast-cancer / logreg / marginal | 30 | 0.895 | 0.878 to 0.911 | class 0 0.91, class 1 0.87; mean set size 0.90 |
| breast-cancer / logreg / class-conditional | 30 | 0.902 | 0.886 to 0.916 | class 0 0.91, class 1 0.89; mean set size 0.91 |
| breast-cancer / lgbm / marginal | 30 | 0.890 | 0.874 to 0.907 | class 0 0.91, class 1 0.85; mean set size 0.91 |
| breast-cancer / lgbm / class-conditional | 30 | 0.894 | 0.878 to 0.911 | class 0 0.91, class 1 0.87; mean set size 0.91 |

Regression intervals (diabetes, Ridge, 100 splits): coverage 0.908 (95% CI 0.901 to 0.915), mean width 196.9.

| Condition | n instances | Validity (recomputed) | Mean features changed | Mean cost (MAD units) | Constraint violations |
|---|---|---|---|---|---|
| synthetic-lending / logreg | 50 | 1.00 | 1.02 | 3.50 | 0 |
| synthetic-lending / lgbm | 50 | 1.00 | 1.28 | 3.01 | 0 |
| adult / logreg | 50 | 1.00 | 1.00 | 6.80 | 0 |
| adult / lgbm | 50 | 1.00 | 1.10 | 1.42 | 0 |
| breast-cancer / logreg | 50 | 1.00 | 1.74 | 14.65 | 0 |
| breast-cancer / lgbm | 50 | 1.00 | 2.34 | 4.93 | 0 |

### E5. User study

**Manipulation:** Explanation vs. explanation + uncertainty (small N)
**Measured:** Decision accuracy, trust calibration

| Condition | n | Result | 95% CI | Notes |
|---|---|---|---|---|
| not run | 0 | none | none | No human participants were available. No data were collected or simulated. H4 is untested. |

## 8. Discussion

**What was supported.** On faithfulness (E1), all three local methods (SHAP, LIME, local permutation) beat the random-order control on essentially every instance in every cell (98% to 100% of instances), with deletion gains of 0.13 to 0.25. A single global ordering from permutation importance is much weaker (gains 0.05 to 0.14), which supports showing local rather than global explanations when the question is "why this prediction". H3 (SHAP more faithful than local permutation) holds in direction in all twelve cell-baseline combinations and is Holm-significant in all twelve, but the effect is small: the mean difference in deletion gain is below 0.01 in nine of twelve (against gains of about 0.15), and the paired dominance statistic ranges from 0.09 to 0.93, so local permutation beats SHAP on many individual instances in several cells. The practical conclusion is that SHAP and local permutation are about equally faithful by this metric on these models. The three larger differences were adult/LightGBM (0.017 with the marginal baseline, 0.041 with the mean baseline) and breast-cancer/LightGBM with the mean baseline (0.029).

**What was not supported.** H1 is not supported as a general claim. For LightGBM, SHAP was *less* stable than LIME on the synthetic lending data (0.904 versus 0.926) and on adult (0.915 versus 0.952), and clearly more stable only on breast-cancer (0.731 versus 0.514; Holm p about 3e-15). With few features and strong signals, LIME's local surrogate was stable in our configuration; with 30 correlated features its sampling noise dominated. That is a conjecture about mechanism that this study did not test. When only the explainer's own randomness varies (one fixed model), SHAP was more stable than LIME on the lending and breast-cancer data and less stable on adult. Local permutation was the least stable method in 8 of 12 dataset-model-setting cells. H2 is not supported either: SHAP and LIME agreed more than "moderate at best" in all but one cell (the upper CI bound was at most 0.70 only for breast-cancer/logistic regression, with overlap 0.65 against a chance level of 0.17), and over the synthetic strata overlap did not fall with correlation (Spearman −0.005 for logistic regression, −0.051 for LightGBM; Holm p 0.46 and 0.31). We caution that this synthetic family has five strongly informative features out of ten, which may make the top-5 easy to recover at every correlation level; H2 remains open for weaker-signal data. The correlated real dataset (breast-cancer, mean absolute correlation about 0.39) did show lower overlap than most uncorrelated ones, but it also differs in dimension and chance level, so it is not evidence of a correlation effect.

**Fairness and calibration (E4).** On adult, all three models showed a demographic-parity gap of about 0.18 to 0.19 and an equalized-odds gap of 0.13 to 0.15 between women and men, flagged in all ten splits, with a near-zero calibration gap (0.003 to 0.005): the models are about equally well calibrated for both groups while selecting very different shares of each, mostly reflecting the base-rate difference in the 1994 labels (11.5% versus 30.0% positive in the first split's test set). This is a property of the data and the task, not a finding about a deployed system. On the synthetic data the validation shows that the audit's *estimates* are accurate where the truth is clearly non-zero: for the oracle predictor the 95% intervals covered the population demographic-parity and equalized-odds gaps in 97% and 100% of replicates at strength 1 (criterion V1 met), and the mean error of the demographic-parity estimate for the trained models was at most 0.004 (V2b met). At strength 0 the false-alarm rate was 0% (V3 met) but interval coverage of the true (near-zero) gap by the oracle audit was only 87% for demographic parity and 0% for equalized odds (V1 not met at strength 0): the max-minus-min gap statistic is biased upward at n=1,800 (by 0.01 to 0.03), so bootstrap intervals exclude a true value near zero. The more important limitation concerns sensitivity. The oracle's planted demographic-parity gap (0.19) was flagged in 100% of replicates (V2 met), but the group-blind trained models inherit only part of it through the proxy feature (population gaps 0.049 to 0.064) and were flagged in only 3% to 13% of replicates by the 0.05 tolerance. The audit measured the gap correctly; the screening threshold was too coarse to flag it. The planted calibration disparity was detected as a larger ECE gap at strength 1 than at strength 0 (V4 met for all three models, mean increase 0.019 to 0.021) but never exceeded the 0.05 tolerance.

**Supporting checks.** Split-conformal sets achieved the nominal 0.90 marginal coverage on lending and adult (0.899 to 0.903) and slightly under it on the small breast-cancer data (0.890 to 0.895, with intervals that include 0.90). Marginal coverage hides large class differences: on adult the positive (minority) class had 0.68 to 0.70 coverage while class-conditional calibration restored about 0.90 for both classes, at the cost of larger sets. Coverage also differed by sex on adult (women 0.94 to 0.95, men 0.87 to 0.88), which the marginal guarantee does not address. Regression intervals on the diabetes data covered 0.908. The counterfactual search returned a valid counterfactual for every one of 300 instances, with no constraint violations, changing 1.0 to 2.3 features on average; validity does not imply the change is realistic or achievable, and costs reached 14.6 median-absolute-deviation units for logistic regression on breast-cancer.

**Practical guidance.** Treat attributions as approximate: show agreement or stability next to them. Report per-class and per-group coverage for conformal sets. Do not rely on a fixed screening threshold to certify fairness; report gaps with intervals and the base-rate context. Choose and report the removal baseline when claiming faithfulness: on adult/LightGBM the mean baseline gave LIME a deletion gain of only 0.009 against 0.126 with the marginal baseline.

## 9. Threats to validity

- **Faithfulness metrics depend on the baseline value used for removal.** Both a mean and a marginal baseline are reported and they sometimes disagree strongly (adult/LightGBM). SHAP and local permutation use the same marginal-replacement idea as the marginal baseline, so the metric may favour them over LIME; a metric not tied to that scheme was not used.
- **Dependence and pseudo-replication.** Instances are explained by the same fitted model, so per-instance confidence intervals and p-values understate uncertainty; with 50 to 400 instances almost any difference is "significant", so effect sizes matter more. Repeated random splits in E4 and S1 overlap and are not independent.
- **Configuration dependence.** LIME used 1,000 samples and quartile discretisation (not its 5,000-sample default); SHAP used a 100-row background; local permutation used 30 rows. Other settings could change H1.
- **Synthetic data.** The synthetic findings concern the generators (a logistic outcome, Gaussian features, a planted penalty and measurement noise); they are not real-world findings. The correlated-features family has strong signal in half of its features.
- **Public datasets carry historical bias** (adult is a 1994 census extract with binary sex); gaps there mix inequality in the labels with model behaviour. The data cards state this.
- **Operational definitions were refined in v0.2 before the run, not pre-registered externally;** the H3 size threshold of 0.01 was chosen after seeing results. There is no version-control tag for the reported run.
- **Small samples:** the breast-cancer conformal and test splits are tiny (86 rows each); the E3 global-agreement intervals resample instances while holding the permutation importance fixed and are narrow.
- **Shared, loaded hardware.** The machine was at 100% CPU load with about 2.9 GB free memory. No reported value is a timing, but multi-threaded floating-point summation can differ slightly between runs; the run was done once.
- **No human study.** Nothing here shows that explanations or uncertainty displays help people. H4 is untested.
- **Researcher degrees of freedom:** analysis choices are fixed in this protocol and in the scripts; deviations are logged in the change log below.

## 10. Reproducibility checklist

- [ ] Code tagged at the commit used for the reported results (the folder is not under version control, so no tag exists)
- [x] Environment pinned (lockfile `uv.lock` and a Python 3.12 pin; no container image is used)
- [x] Random seeds and run counts recorded (master seed 20240611; per-experiment config files in `results/`)
- [x] Raw results published as CSV in `results/` (no analysis notebook; the report tables are rendered by a script)
- [x] Hardware or service configuration described (CPU model string, core counts, memory and load; see Environment)
- [ ] One command reproduces every figure and table (one command reproduces all results and a second renders the tables; the report prose is hand-written)
- [x] Data sources, licences and versions listed (Environment and `results/data_manifest.json`)

## Verification

Everything in this report can be regenerated from the source code. From the repository root, after installing with `uv sync`:

- Tests, lint and types: `uv run pytest`, `uv run ruff check .`, `uv run mypy`
- All experiments (about 70 minutes on two busy cores, writes to `results/`): `uv run python -m experiments.run_all`
- Individual experiments: `uv run python -m experiments.e1`, `e2`, `e3`, `e4`, `checks`, `model_cards`
- Report tables: `uv run python -m experiments.report_tables` (writes `results/report_tables.md`)

A tiny end-to-end check of the pipeline is available as `uv run python -m experiments.run_all --smoke` (set `XAI_RESULTS_DIR` to a scratch folder so it does not overwrite `results/`).

## 11. References

- Lundberg & Lee (2017). A Unified Approach to Interpreting Model Predictions. NeurIPS.
- Ribeiro, Singh & Guestrin (2016). "Why Should I Trust You?": Explaining the Predictions of Any Classifier. KDD.
- Mitchell et al. (2019). Model Cards for Model Reporting. FAT*.
- Hardt, Price & Srebro (2016). Equality of Opportunity in Supervised Learning. NeurIPS.
- Angelopoulos & Bates (2021). A Gentle Introduction to Conformal Prediction and Distribution-Free Uncertainty Quantification. arXiv:2107.07511.
- Petsiuk, Das & Saenko (2018). RISE: Randomized Input Sampling for Explanation of Black-box Models. BMVC.
- Hooker, Erhan, Kindermans & Kim (2019). A Benchmark for Interpretability Methods in Deep Neural Networks. NeurIPS.
- Alvarez-Melis & Jaakkola (2018). On the Robustness of Interpretability Methods. arXiv:1806.08049.
- Krishna et al. (2022). The Disagreement Problem in Explainable Machine Learning: A Practitioner’s Perspective. arXiv:2202.01602.
- Wachter, Mittelstadt & Russell (2017). Counterfactual Explanations without Opening the Black Box. Harvard Journal of Law & Technology.
- Okabe & Ito (2008). Color Universal Design (the colour-blind-safe palette used in the dashboard).

> These citations were written from memory while building the project and have **not** been re-checked against the original sources; verify each one before relying on or publishing them.

## 12. Change log

| Date | Version | Change |
|---|---|---|
| (blueprint) | v0.1 | Protocol drafted; no results yet. |
| 2026-10-22 | v0.2 | Protocol made operational and results added. Before the full run: (a) operational definitions for H1, H2 and H3, the primary baseline and the Holm families were fixed (section 2); (b) the audit-validation criterion V2 was **revised** after a pre-run design check of the synthetic generator (one seed, population sample, no experiment output used) showed that group-blind models inherit a demographic-parity gap of only about 0.05 to 0.06 of the planted 0.14 to 0.19: the original wording required flagging in 90% of replicates for the trained models; the revised V2 applies to the oracle, and the trained models are reported descriptively with V2b (estimate accuracy). The 0.05 tolerance itself was not changed. (c) The E4 part A bootstrap size was set to 200 resamples. Development used tiny smoke-size runs of every experiment to debug the scripts (not reported, not kept). The full experiment set was run **once** (`run_all`, 4,035 s); no experiment was rerun and no result was discarded. After the run, only reporting code was added (`experiments/report_tables.py`, including a clamp of Wilson lower bounds at zero in the rendered tables) and documentation was written. The H3 size threshold (0.01) and the reading of H2a were applied after seeing results and are flagged as descriptive. E5 was not run; H4 is untested. |

---

[README](../README.md) · [Client case study](case-study.md) · [Research report](research-report.md)
