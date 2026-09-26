# S&P 500 Volatility Forecasting & Variance Risk Premium (VRP)

An econometric study investigating whether the S&P 500 implied volatility (IV) surface provides incremental predictive power for future 21-day realized volatility over pure historical-return benchmarks.

---

## Executive Summary
- **Core Question:** Does option-implied information improve forward realized volatility forecasts compared to time-series models (HAR-RV, EGARCH)?
- **Key Finding:** While option-implied features (skew, convexity, term structure) significantly boost *in-sample* fit ($R^2$ up to ~22%), simpler historical models (**HAR-RV**) demonstrate superior *out-of-sample* robustness due to overfitting during regime shifts (e.g., Feb 2018 "Volmageddon").
- **Economic Insight:** The Variance Risk Premium ($\text{IV}^2 - \text{RV}^2$) remains structurely positive, reflecting an investor insurance premium against market crash risk.

---

## Key Results & Insights

- **Predictive Power of Options (In-Sample):** Incorporating implied volatility surface features (ATM IV, skewness, convexity, and term structure spread) significantly enhances explanatory power, boosting $R^2$ from 4.6% (HAR benchmark) to ~22% in-sample.
- **Out-of-Sample Superiority of Simpler Models:** Despite lower in-sample fit, the 3-parameter **HAR-RV** model delivers the best out-of-sample forecasts (lowest RMSE and QLIKE). Richer specifications suffer from overfitting when evaluated on unseen data.
- **Structural Information Overlap:** Hybrid models ($\text{HAR-IV}$, $\text{HAR-PCA}$) reveal that option-implied information largely overlaps with recent realized volatility dynamics, offering little statistically significant incremental value beyond the HAR structure.
- **Regime Shift & Volmageddon Sensitivity:** The February 2018 volatility spike caused a massive distribution shift (variance components doubled). Over-parameterized models extrapolated poorly outside their training domain, highlighting the critical impact of market regimes on model stability.
- **Economic Value of VRP:** The Variance Risk Premium ($\text{IV}^2 - \text{RV}^2$) remains positive on average, consistent with an insurance premium for downside tail risk. A simple Variance Swap trading strategy built on model signals yields an attractive Sharpe ratio (~2.20) during post-spike normalization phases.

---

## Key Methodology & Workflow

1. **Option Data & Feature Engineering (`Features_Engineering.ipynb`):**
   - Implied Spot Rate & Discount Curve via Put-Call Parity regressions (Brenner & Galai, 1986).
   - Black-Scholes IV inversion using Brent’s root-finding algorithm.
   - 30-day fixed-horizon ATM IV, Skew, and Convexity via **variance-space linear interpolation**.
   - Structural overlap correction using **Newey-West HAC standard errors (20 lags)** for daily 21-day forward realized volatility.

2. **Econometric Modeling (`Econometrics_Models.ipynb`):**
   - **Historical Benchmarks:** Historical Mean, HAR-RV (Corsi, 2009), EGARCH(1,1).
   - **Implied Models:** Nested OLS, Mincer-Zarnowitz unbiasedness test, VAR/VECM robustness checks.
   - **Surface Analysis:** Panel Data with Expiry Fixed Effects (Hausman test) and PCA decomposition (Level, Slope, Curvature factors).
   - **Hybrid Models:** HAR-IV & HAR-PCA specifications.
   - **Economic Application:** Systematic Variance Swap trading strategy backtest.

---

## Repository Structure
```text
├── data/                       # Daily SPX option chains (CSV)
├── utilities/                  # Modular Python helpers (IV engine, Econometrics)
├── Features_Engineering.ipynb # Dataset cleanup & IV Surface construction
├── Econometrics_Models.ipynb   # In-Sample & Out-of-Sample econometric horse race
└── time_series.pkl            # Processed features time series