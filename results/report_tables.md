## E1

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

## E2

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

## E3

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

## E4

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

## Supporting checks S1 and S2

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
