---
layout: page
title: Time Series Representation Learning
permalink: /projects/time-series-representation/
---

# Deep Time Series Representation Learning (EDF Lab & L'Oréal)

**Research Internship Projects (2019–2020)** | Focus on smart-meter load curves and industrial forecasting.

---

## Project 1: Smart-Meter Classification (EDF Lab)

### Objective
Building a benchmark for classifying domestic hot-water (ECS) and heating usage from long-horizon smart-meter load curves (30-min sampling).

### Technical Approach
- **Deep Sequence Models**: Implemented and compared modern architectures including **ResNet**, **InceptionTime**, **Causal Dilated ConvNets**, and **ConvLSTM**.
- **Unsupervised Representations**: Developed and evaluated **Contractive Autoencoders (CAE)**, **LSTM-AE**, and **VAEs** with pseudo-label self-supervision.
- **Self-Supervised Learning**: Matched end-to-end ConvLSTM accuracy using CAE-learned representations followed by a shallow classifier, which **cut training time by ~30$\times$**.

### Results
- Achieved **94%+ accuracy** via ensembling and the inclusion of datetime-aware features.
- Successfully handled missing timestamps via NaN-aware preprocessing and windowing across seasonal splits (1W vs 2W windows).

---

## Project 2: Goods-to-Tester Transformation (L'Oréal Paris)

### Objective
Modeling the transformation of retail goods into testers as store/city-level time series to optimize inventory.

### Technical Approach
- **Feature Engineering**: Engineered compact time-series descriptors (variability, peaks, approximate entropy) using **tsfresh**.
- **Stochastic Augmentation**: Proposed a novel stochastic time-warp augmentation (cubic-spline distortion) to synthesize realistic trajectories while preserving cluster structure.
- **Forecasting & Costs**: Built **ARMA/ARIMA** and **LSTM** baselines with AIC-based selection. Introduced a **cost-sensitive loss** to handle the specific trade-offs between over and under-production.

### Results
- Identified store archetypes through bench-marked clustering (k-means++, GMM, spectral).
- Provided actionable seasonality diagnostics and out-of-stock drivers via dependence tests (Cram\'er's $V$).

---

## Technical Stack
- **Deep Learning**: PyTorch, TensorFlow
- **Analytics**: scikit-learn, statsmodels, tsfresh, SciPy
- **Domain**: Smart Grids, Supply Chain, Time Series Forecasting
