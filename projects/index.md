---
layout: page
title: Projects
permalink: /projects/
---

# Projects

Selected work spanning causal inference, deep learning, and applied ML systems.

---

## Research

### Causal Contrastive Learning for Counterfactual Regression
**NeurIPS 2024** · [Paper](https://openreview.net/forum?id=bKOZYBJE4Z) · [Blog Post](/blog/causal-contrastive-learning)

Counterfactual regression over long time horizons using contrastive objectives and information-theoretic regularization. Addresses selection bias in longitudinal observational data.

`causal-inference` `contrastive-learning` `time-series` `PyTorch`

---

### Causal Dynamic VAE for Longitudinal Treatment Effects
**TMLR 2025** · [Paper](https://openreview.net/forum?id=atf9q49DeF) · [Blog Post](/blog/causal-dvae)

Variational autoencoder for inferring unobserved risk factors from outcome sequences. Provides identifiability results and generalization bounds for individual treatment effect estimation.

`causal-inference` `variational-inference` `identifiability` `PyTorch`

---

## Industry

### LLM-Based Contract Compliance System
**Saint-Gobain, 2024–Present** · [Blog Post](/blog/llm-contract-compliance)

RAG-based system for evidence-grounded checklist verification. Improved decision stability from ~10% to <1% variance across runs using hybrid similarity ranking and caching.

`LLM` `RAG` `pgvector` `PostgreSQL` `FastAPI`

---

### Bayesian Predictive Maintenance
**Saint-Gobain, 2024–Present** · [Blog Post](/blog/bayesian-maintenance)

Failure-risk estimation using Pyro. Posterior uncertainty guides maintenance triage across 3 sites and 5 production lines.

`Bayesian` `Pyro` `time-series` `calibration`

---

### Customer Segmentation Pipeline
**Saint-Gobain, 2021** · [Blog Post](/blog/customer-segmentation)

Unsupervised segmentation for commercial targeting. Reduced runtime by 85% and improved click-through by 40%.

`clustering` `feature-selection` `UMAP` `scikit-learn`

---

### Time-Series Representation Learning
**EDF Lab, 2020** · [Blog Post](/blog/time-series-representation)

Representation learning (VAE, GAN) for electricity consumption time series. Improved downstream classification by 5% over CNN/LSTM baselines.

`representation-learning` `VAE` `GAN` `time-series` `PyTorch`
