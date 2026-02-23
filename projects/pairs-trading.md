---
layout: page
title: Market-Neutral Pairs Trading
permalink: /projects/pairs-trading/
---

# Market-Neutral Pairs Trading (Stat-Arb)

This project implements an end-to-end market-neutral statistical arbitrage pipeline, focusing on pair selection, spread modeling, and robust backtesting.

---

## Overview

The goal is to identify pairs of assets that exhibit a stable cointegration relationship and trade the mean-reverting spread between them. The system is designed to be market-neutral, neutralizing exposure to broad market factors.

## Key Components

### 1. Robust Pair Selection
To mitigate data-mining risk, I implemented a multi-stage screening process:
- **Liquidity Filtering**: Ensuring assets meet minimum volume thresholds.
- **Cointegration Testing**: Using Johansen and Engle-Granger tests with multiple-testing control (Benjamini-Hochberg).
- **Stability Screening**: Out-of-sample verification of half-life stability and hedge-ratio persistence.

### 2. Spread Modeling (Ornstein-Uhlenbeck)
The spread $X_t$ is modeled as a mean-reverting OU process:
$$dX_t = \theta(\mu - X_t)dt + \sigma dW_t$$
- **Hedge Ratio Estimation**: Calculated via Total Least Squares (TLS) to account for errors in both variables.
- **Signal Generation**: Entry/exit thresholds derived from the equilibrium mean $\mu$ and stationary variance.

### 3. Portfolio Construction
- **Explicit Constraints**: Position caps, neutrality targets, and bounded factor exposures.
- **Factor Neutralization**: Residualizing returns against common risk factors (Size, Value, Momentum).

---

## Performance & Backtesting

I implemented a high-fidelity walk-forward backtester that accounts for:
- **Execution Frictions**: Commission costs, slippage, and fill delay.
- **Leverage Management**: Dynamic margin limits and position sizing based on volatility.

### Results summary
- **Risk Metrics**: Stress-tested under various cost and liquidity regimes.
- **Diagnostics**: Detailed reporting on turnover, drawdowns, and factor exposure.

---

## Technical Stack

- **Languages**: Python
- **Libraries**: NumPy, pandas, SciPy, statsmodels, Matplotlib
- **Infrastructure**: Automated data ingestion and broker execution simulation.

[View the Code on GitHub](https://github.com/moad-lihoconf/ou_pair_trading)
