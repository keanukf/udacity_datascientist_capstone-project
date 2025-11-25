# Predicting Starbucks Customer Spendings

## Optimizing rewards offers with rigorous statistical modeling

This capstone replicates a realistic applied research workflow for the Udacity Data Science Nanodegree. I treat the Starbucks mobile rewards simulator as a longitudinal field experiment and show how careful data engineering, transparent modeling, and quantitative model critique can be combined to generate credible business recommendations.

- Read the long-form write-up on [Medium](https://keanu-forthmann.medium.com/predicting-starbucks-customer-spendings-cca39b7533c2).
- The notebook `Starbucks_Capstone_notebook.ipynb` contains the complete exploratory analysis, model code, and diagnostic plots.
- Data lives in `data/` (three JSON files from Udacity) and figure assets in `images/`.

---

## 1. Project framing

### 1.1 Motivation

Starbucks uses a mobile loyalty app to issue targeted offers. Because offers incur costs (discounts, free drinks, marketing bandwidth), the company needs an evidence-based policy for when to send which offer. The dataset provided by Udacity simulates the app’s event stream—customer demographics, offers received/viewed/completed, and spending transactions. The research goal is to predict individual-level spend and isolate which demographic or behavioral factors drive high-value engagement.

### 1.2 Research questions

1. Which measurable attributes (demographics, tenure, engagement, offer history) are most predictive of a customer’s average spend?
2. Which offer families (BOGO, discount, informational) meaningfully shift spend once the customer receives and views them?
3. How can we characterize customer segments by their spending trajectories and offer sensitivity?

### 1.3 Evaluation metrics

Because spend is continuous, I cast the problem as a regression task. Model quality is reported using:

- Coefficient of determination (R²) on stratified train/test splits.
- Cross-validated generalization scores when hyper-parameters are tuned (Ridge/Lasso).
- Statistical significance of coefficients (p-values) to quantify confidence in each factor’s contribution.

---

## 2. Data resources

| File | Description |
|------|-------------|
| `portfolio.json` | Offer metadata: difficulty, reward, duration, delivery channels, categorical offer type. |
| `profile.json` | Customer demographics: age, gender (including “Other” and missing), account creation date, income. |
| `transcript.json` | Event log: transactions, offers received/viewed/completed, timestamps, spend amounts, offer identifiers. |

Key schema nuances:

- Offer identifiers appear both as `offer id` and `offer_id`, so custom parsing logic standardizes them.
- Timestamps are recorded in hours since experiment start. I convert them to days to enable temporal reasoning.
- The simulator produces 17k customers and 306k events with sparse missingness limited to gender/income.

---

## 3. Methodology

### 3.1 Data engineering

1. **Target construction** – Filter `transcript.json` for transaction events, extract monetary amounts, and aggregate spend per customer.
2. **Feature synthesis** – Merge aggregated spend with demographics, tenure (`member_days`), engagement ratios (viewed vs. received), transaction counts, and an offer influence matrix capturing “received → viewed → completed” sequences for every offer.
3. **Categorical handling** – Encode gender (including missing values) using one-hot vectors to preserve behavioral differences without imposing ordinal assumptions.
4. **Diagnostics** – Visualize distributions (income, age, tenure, spend) to detect artifacts (e.g., placeholder ages of 118). Remove or reinterpret anomalous values via domain-aware cleaning.

### 3.2 Modeling strategy

- **Baseline Linear Regression** – Provides interpretable coefficients and a transparent starting point. Achieves ~0.28 R² on held-out data using demographics + engagement + offer history.
- **Collinearity management** – Apply Variance Inflation Factor (VIF) analysis iteratively to prune redundant signals (e.g., male/female dummies, influenced-offer sum vs. per-offer counts, member_days highly correlated with tenure clusters). This enforces numerical stability and guards against spurious attributions.
- **Regularization** – Deploy Ridge (L2) and Lasso (L1) regressors with 5-fold cross-validation to balance bias/variance and explore automatic feature selection. Grid search over 50 alpha values surfaces an optimal Ridge alpha ≈ 14.7 (non-normalized input) and demonstrates how coefficient shrinkage affects interpretability.

### 3.3 Interpretation framework

- Standardize coefficients to compare heterogeneous scales (income vs. binary gender indicators).
- Rank features by absolute impact to highlight levers with the largest marginal effect on spend.
- Examine bimodal spend distribution to reason about latent customer archetypes (low-engagement habitual users vs. high-spend enthusiasts).

---

## 4. Findings

### 4.1 Determinants of spend

- Income is the dominant positive predictor, consistent with consumer purchasing power theory.
- Female-identified customers and those who submitted gender information tend to spend more, hinting at self-selection effects in profile completeness.
- Transaction count and specific BOGO offers (reward/difficulty pairs of 5 and 10) signal high engagement and correlate with elevated spend.
- Informational offers contribute little to incremental revenue; their coefficients remain negligible or negative after controlling for demographics and behavior.

### 4.2 Offer influence dynamics

- Customers average 1.28 influenced offers (received + viewed + completed). Influence rates are skewed: a subset consistently engages with every offer, while others almost never convert.
- Viewing rate (viewed/received) clusters near 0.76, suggesting that once an offer is delivered, three quarters are actually read—supporting the focus on high-quality offer design.
- The user-offer matrix exposes which BOGO combinations drive recurrent completions, providing a blueprint for targeted experimentation.

### 4.3 Model performance

- Linear regression after VIF pruning: R² ≈ 0.278 on test data with statistically significant coefficients (low p-values).
- Ridge regression (α = 14.69): modest uplift to R² ≈ 0.297 while dampening overfitting.
- Lasso regression (α = 0.01): zeroes out weaker predictors (e.g., income or transaction count under certain regularization strengths), demonstrating the sensitivity of automated feature selection and motivating domain-guided validation.

---

## 5. Limitations and future work

1. **Model class** – Only linear models were explored. Non-linear learners (gradient boosting, random forests, causal forests) could capture higher-order interactions between offers and demographics.
2. **Temporal reasoning** – The current setup aggregates spend over the full horizon. Sequence models or survival analysis could better capture how offer timing affects near-term spend.
3. **Outlier treatment** – Regularization mitigates leverage points but explicit robust regression or isolation forest outlier detection could further stabilize coefficients.
4. **Hyperparameter granularity** – Alpha grids were coarse. Bayesian optimization or nested CV could fine-tune regularization strength with tighter confidence intervals.
5. **Behavioral segmentation** – Clustering based on the user-offer matrix plus spend trajectories could reveal latent personas for personalization beyond simple regression coefficients.

---

## 6. Repository guide

- `README.md` – high-level study overview (this document).
- `Starbucks_Capstone_notebook.ipynb` – executable research notebook containing EDA, feature engineering, model training, and visualization exports.
- `data/portfolio.json` – metadata for 10 simulated offers.
- `data/profile.json` – demographics and membership dates for 17k customers.
- `data/transcript.json` – 306k event logs (transactions and offer lifecycle events).
- `images/` – selected plots exported from the notebook for reporting.

---

## 7. How to reproduce

1. Install dependencies (Python ≥3.8, `pandas`, `numpy`, `scikit-learn`, `plotly`, `statsmodels`, `matplotlib`, `seaborn`).
2. Launch the notebook and run sequentially; each section is annotated with markdown cells that mirror this README.
3. To extend the analysis, drop new models into the modeling section or add additional features to the engineered dataset before the regression pipeline.

---

## 8. Contact

If you have questions about the methodology or would like to discuss extending this work (e.g., causal inference, reinforcement learning for offer policies), feel free to reach out via the contact info on my GitHub profile. I'm particularly interested in collaborations at the intersection of econometrics, ML systems, and responsible personalization.
