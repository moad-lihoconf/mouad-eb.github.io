---
layout: page
title: Bayesian Predictive Maintenance
permalink: /projects/bayesian-maintenance/
---

# Bayesian Predictive Maintenance & Root-Cause Analysis (Saint-Gobain)

**Senior Data Scientist Project (2024–Present)** | Implemented across 3 plants and 5 production lines.

---

## Objective: Calibrated Risk Triage

Moving beyond simple binary classification, this project aims to produce **calibrated failure probabilities** and identify the localized drivers of asset failure in complex industrial environments.

## Technical Approach

### 1. Intensity-Based Risk Modeling
Instead of standard classification, I built an end-to-end failure-risk pipeline using **Poisson/Cox-style event intensities**.
- **Hazard Modeling**: Trained in **Pyro** (Variational Inference) to handle asset-day exposure and censoring-aware at-risk datasets.
- **Calibration**: Focused on achieving high Brier scores to ensure probabilities represent real-world risk, enabling meaningful prioritization for maintenance teams.

### 2. Hierarchical Root-Cause Analysis (RCA)
Localization of failure drivers is performed through mixed-effects modeling:
- **Spatial Hierarchy**: Quantifying risk heterogeneity across Planet $\rightarrow$ Line $\rightarrow$ Workcenter $\rightarrow$ Equipment levels.
- **Sensor Attribution**: Driven by sensor-stream covariate attribution to identify anomalous drifts before breakdown.

### 3. Data Engineering & Lineage
Harmonized machine stops, scrap KPIs, work orders, and high-frequency sensor streams.
- **Matching**: Achieved **98% entity matching** across heterogeneous data warehouses.
- **Validation**: Automated lineage and automated QA to ensure "leakage-safe" training sets.

## Results

- **Forecast Horizon**: Reliable **7-day** failure-risk forecasts.
- **RCA Insights**: Successfully localized failure drivers for persistent downtime events, leading to targeted equipment upgrades.
- **Scalability**: Deployed to production monitoring for 5 major production lines.

---

## Technical Stack
- **Modeling**: Python, PyTorch/Pyro, scikit-learn, statsmodels
- **Data**: SQL (PostgreSQL), Snowflake, DuckDB
- **Automation**: MLflow for experiment tracking and model registry
