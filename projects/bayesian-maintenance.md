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
Instead of standard classification, I built an end-to-end failure-risk pipeline using **Poisson/Cox-style event intensities** on asset-day exposure to produce calibrated failure probabilities.
- **Hazard Modeling**: Trained in **Pyro** (Variational Inference) to handle asset-day exposure and a leakage-safe, censoring-aware at-risk dataset.
- **Calibration**: Focused on achieving high Brier scores to ensure probabilities represent real-world risk, enabling meaningful prioritization for maintenance teams.

### 2. Hierarchical Root-Cause Analysis (RCA)
Developed sensor-driven root-cause analysis to localize failure drivers and heterogeneity across plant, line, workcenter, and equipment:
- **Spatial Hierarchy**: Quantifying risk heterogeneity across Planet $\rightarrow$ Line $\rightarrow$ Workcenter $\rightarrow$ Equipment levels using **mixed effects modeling**.
- **Sensor Attribution**: Driven by sensor-stream covariate attribution to identify anomalous drifts before breakdown.

### 3. Data Engineering & Lineage
Harmonized machine stops, scrap KPIs, work orders, and high-frequency sensor streams across multiple warehouses.
- **Matching**: Achieved **98% entity matching** across heterogeneous data sources with automated QA/lineage.
- **Verification**: Built a validated RCA dataset across 3 sites and 5 lines.

## Results

- **Forecast Horizon**: Reliable **7-day** failure-risk forecasts.
- **RCA Insights**: Successfully localized failure drivers for persistent downtime events, leading to targeted equipment upgrades.
- **Scalability**: Deployed to production monitoring for 5 major production lines.

---

## Technical Stack
- **Modeling**: Python, PyTorch/Pyro, scikit-learn, statsmodels
- **Data**: SQL (PostgreSQL/pgvector), Snowflake, DuckDB
- **Automation**: MLflow for experiment tracking and model registry
