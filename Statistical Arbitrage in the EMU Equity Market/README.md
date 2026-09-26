# Eigenportfolio Statistical Arbitrage on EURO STOXX 50

An econometric and systematic quantitative framework implementing an **Eigenportfolio-based Statistical Arbitrage strategy** on the constituents of the EURO STOXX 50 index (`SX5E`), strictly following the methodology established by **Marco Avellaneda and Jeong-Han Lee (2008)** (*"Statistical Arbitrage in the U.S. Equities Market"*).

---

## Executive Summary

- **Core Question:** Can systematic equity market-neutral returns be extracted from short-term idiosyncratic pricing deviations among EURO STOXX 50 constituents using PCA factor decomposition and Ornstein-Uhlenbeck (O-U) process modeling?
- **Key Finding:** The PCA-based factor model efficiently captures systematic market risk, isolating stationary, mean-reverting residual processes. Filtering for fast mean-reverting assets ($\kappa > 8.4$, half-life $< 30$ days) successfully generates profitable long/short market-neutral trading signals with strong risk-adjusted performance.
- **Economic Insight:** Stock returns evaluated in "trading time" (volume-adjusted) amplify informational signals during low-volume moves, improving the signal-to-noise ratio for $s$-score estimation and mean-reversion entry/exit timing.

---

## Key Results & Insights

- **Systematic Risk Decomposition (PCA):** Extracting the primary principal components from the asset correlation matrix effectively isolates systemic sector and market signals, leaving orthogonal residual series suitable for statistical arbitrage.
- **Ornstein-Uhlenbeck Dynamics:** Residual cumulative returns display distinct mean-reversion characteristics. Fitting an O-U process via discrete AR(1) regressions provides robust analytical estimates for drift, equilibrium variance ($\sigma_{eq}$), and speed of mean reversion ($\kappa$).
- **Dynamic $s$-Score Generation:** Standardized $s$-scores accurately trigger long ($s < -1.25$) and short ($s > +1.25$) entry signals when prices deviate from their equilibrium level $m$, closing positions when the signal normalizes ($\vert{}s\vert{} \le 0.50$).
- **Strict Filtering & Risk Control:** Imposing speed-of-reversion thresholds ($\kappa > 8.4$) filters out persistent structural trends, ensuring the strategy only engages in short-horizon, high-probability statistical arbitrage.
- **Out-of-Sample Performance:** The resulting market-neutral, dollar-balanced portfolio exhibits low correlation to the overall market benchmark, offering consistent Sharpe efficiency after accounting for rebalancing turnover and transaction costs.

---

## Key Methodology & Workflow

1. **Volume-Adjusted Returns & Feature Engineering (`statistical_arbitrage.py`, `covariance_utilities.py`):**
   - Rescaling returns by volume dynamics ($R_{i,t}^{\text{adj}} = R_{i,t} \cdot \frac{\langle \Delta V_i \rangle}{V_{i,t}}$) to capture "trading time".
   - Rolling estimation window preparation and coverage filtering.

2. **Factor Decomposition & Residual Isolation (`principal_component_analysis.py`):**
   - PCA extraction on standardized returns to build eigenportfolios and isolate residual risks ($\varepsilon_{i,t}$) via OLS regressions ($R_{i,t} = \alpha_i + \sum \beta_{i,j} F_{j,t} + \varepsilon_{i,t}$).
   - Rolling eigenvector alignment to maintain factor continuity.

3. **Ornstein-Uhlenbeck Estimation & Signal Engine (`statistical_arbitrage.py`):**
   - Modeling cumulative residuals $X_i(t)$ as continuous O-U processes via discrete AR(1) regressions.
   - Derivation of parameters ($\kappa, m, \sigma_{eq}$) to compute standard and drift-modified $s$-scores ($s_t = \frac{X_t - m}{\sigma_{eq}}$).

4. **Backtesting & Portfolio Engine (`backtest.py`):**
   - Long/short (+1, -1, 0) dollar-neutral position generation based on $s$-score thresholds.
   - Out-of-sample execution with transaction costs, turnover adjustments, and strategy analytics (Sharpe, Drawdown, Volatility).

---

## Repository Structure

```text
├── data/                         # Market price, volume, and constituent metadata CSVs
├── utilities/                    # Modular Python backend (PCA, O-U engine, Backtest)
├── ex4a_notebook.ipynb           # Main execution notebook and strategy analysis
└── README.md                     # Project documentation