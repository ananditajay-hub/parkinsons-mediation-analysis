# parkinsons-mediation-analysis
Causal mediation analysis of clinical and psychological predictors of Parkinson's Disease severity using R
# Clinical Predictors of Parkinson’s Disease Severity: A Causal Mediation Analysis

## Overview
This repository contains a statistical analysis of a clinical dataset comprising diagnosed Parkinson's patients ($N = 1304$). The objective of this project is twofold:
1. To evaluate the predictive power of baseline physiological metrics and psychological indicators on motor severity (UPDRS) and cognitive impairment (MoCA).
2. To explicitly model the causal pathways between chronic stress (proxied by sleep disorders), clinical depression, and motor severity using a formal mediation framework.

## Dataset & Preprocessing
The analysis utilizes the "Parkinson's Disease Dataset" sourced from Kaggle. 
* **Initial Cohort:** ~2,100 records.
* **Inclusion Criteria:** Filtered strictly for patients with a positive Parkinson's diagnosis.
* **Missing Data:** Rows containing `NA` values in the target variables (Sleep Disorders, Depression, UPDRS, Age, BMI) were dropped via listwise deletion to satisfy the strict missing-data assumptions of the `mediation` package.
* **Final Analyzed Cohort:** $N = 1304$.

## Statistical Methodology

All statistical modelling and exploratory data analyses were conducted in **R** (`dplyr`, `tidyr`, `ggplot2`, `mediation`).

### 1. Baseline Predictive Modelling
* **Linear Regression:** A multivariable linear model to predict continuous UPDRS scores, adjusting for Age, BMI, Systolic BP, Depression, and Sleep Disorders.
* **Logistic Regression:** A binomial logistic regression model evaluating predictors of cognitive impairment (engineered as binary: MoCA < 26), incorporating motor (Tremor, Rigidity) and psychological factors.

### 2. Causal Mediation Analysis
To test the hypothesis that clinical depression mediates the relationship between chronic sleep disorders (stress proxy) and motor severity, a formal mediation analysis was conducted. 

Let $X$ represent the treatment (Sleep Disorders), $M$ the mediator (Depression), $Y$ the outcome (UPDRS), and $C$ a vector of baseline covariates (Age, BMI). We fit the following structural equations:

**The Mediator Model (Path A):**
$$M = \alpha_0 + \alpha_1 X + \alpha_2 C + \epsilon_1$$
*(Fitted via Binomial Logistic Regression)*

**The Outcome Model (Paths B and C'):**
$$Y = \beta_0 + \beta_1 X + \beta_2 M + \beta_3 C + \epsilon_2$$
*(Fitted via Ordinary Least Squares)*

The **Average Causal Mediation Effect (ACME)** (the indirect effect via depression) and the **Average Direct Effect (ADE)** (the direct effect of sleep disorders on UPDRS) were estimated using Monte Carlo bootstrapping with 500 simulations to generate robust nonparametric confidence intervals.

## Results

### Predictive Modelling
Both the multivariable linear and logistic regressions yielded low explanatory power ($R^2 < 0.01$). Standard baseline physiological metrics (BMI, BP) were surprisingly weak predictors of UPDRS variance in this specific cohort. In the logistic model, only Tremor was a statistically significant predictor of cognitive impairment ($p = 0.024$), albeit with a counterintuitive negative coefficient ($\beta = -0.53$).

### Mediation Analysis Results
* **ACME (Indirect Effect):** Estimate = -0.21, 95% CI [-0.71, 0.13], $p = 0.320$.
* **ADE (Direct Effect):** Estimate = -6.00, 95% CI [-12.34, -0.31], $p = 0.048$.
* **Total Effect:** Estimate = -6.22, 95% CI [-12.40, -0.59], $p = 0.040$.

**Interpretation:** The ACME was not statistically significant, indicating that depression does *not* mediate the pathway between sleep disorders and UPDRS severity in this dataset. However, a significant ADE was observed, suggesting that sleep disorders impact motor severity independently of depressive symptoms. 

These findings highlight the importance of distinguishing between correlation and mechanistic pathways in clinical data analysis.

## Limitations & Future Directions
1. **Cross-Sectional Constraints:** The data represents a single cross-sectional snapshot. True causal inference is limited without longitudinal progression markers.
2. **Unmeasured Confounding:** The low $R^2$ values and the mathematically counterintuitive ADE direction strongly suggest the presence of unobserved confounders. UPDRS severity and cognitive decline are likely driven by higher-dimensional biological realities (e.g., genetic architecture, eQTLs, or transcriptomic profiles) not captured in standard physiological exams.
3. **Future Work:** This highlights the necessity of integrating multi-omics data with clinical metrics to uncover genuine mechanistic pathways in neurodegenerative diseases.
4. ## Key Takeaway

This analysis demonstrates how statistical modelling alone may be insufficient to uncover disease mechanisms without accounting for latent biological complexity.

## Usage
To replicate this analysis:
1. Clone this repository.
2. Ensure R is installed along with the `dplyr`, `ggplot2`, `tidyr`, and `mediation` packages.
3. Run `analysis.R` to execute data preprocessing, model fitting, and the Monte Carlo simulations.
