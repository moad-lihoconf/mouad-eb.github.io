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
**NeurIPS 2024** · [Full Write-Up]({{ '/blog/causal-contrastive-learning/' | relative_url }}) · [Paper](https://openreview.net/forum?id=bKOZYBJE4Z)

Counterfactual regression over long horizons using **contrastive information-theoretic regularization** to capture long-term dependencies and reduce longitudinal selection bias.

`causal-inference` `contrastive-learning` `information-theory` `PyTorch`

---

### Causal Dynamic VAE for Longitudinal Treatment Effects
**TMLR 2025** · [Full Write-Up]({{ '/blog/causal-dvae/' | relative_url }}) · [Paper](https://openreview.net/forum?id=atf9q49DeF)

Variational autoencoder for inferring unobserved risk factors from outcome sequences. Provides **identifiability results** and **generalization guarantees** for individual treatment effect estimation.

`causal-inference` `variational-inference` `identifiability` `PyTorch`

---

### Topological Residual Asymmetry (TRA)
**ICML 2026 (Under Review)** · [Case Study]({{ '/projects/tra-causal/' | relative_url }}) · [arXiv](https://arxiv.org/abs/2602.00427)

Bivariate causal direction detection using **random geometric graphs** and **0-dim persistent homology**. Includes principled abstention under confounding.

`causal-discovery` `TDA` `persistent-homology` `python`

---

### Non-Linear Mixed-Effect Causal Modeling
**JdS 2023 & Causal@UAI 2024** · [Full Write-Up]({{ '/blog/mixed-effect-causal/' | relative_url }}) · [Paper](https://jds2023.sciencesconf.org/resource/page/id/19)

Connecting unobserved risk factors with random effects in **deep non-linear mixed-effects models**. Includes interpretability via **sparse subspace clustering** on decoder Jacobians.

`causal-inference` `mixed-effects` `representation-learning` `UAI`

---

### Toward Transparent Causal Representation Learning
**Research / Work in Progress** · [Full Write-Up]({{ '/blog/transparent-crl/' | relative_url }})

Making learned latent causal factors interpretable by clustering **decoder Jacobians** with sparse subspace methods. Applied to high-dimensional longitudinal tabular data.

`causal-representation-learning` `interpretability` `sparse-modeling` `PyTorch`

---

### Market-Neutral Pairs Trading (Quant Stat-Arb)
**Quant Research Side Project** · [Full Write-Up]({{ '/blog/pairs-trading/' | relative_url }}) · [Code](https://github.com/moad-lihoconf/ou_pair_trading)

End-to-end stat-arb pipeline: spread modeling via **Ornstein-Uhlenbeck (OU)** processes, **TLS hedge-ratio** estimation, and factor-neutralization. Reframed as a long-only convex portfolio problem.

`quantitative-finance` `stochastic-processes` `stat-arb` `Python`

---

### DynSCM: Synthetic Causal Priors for Forecasting Foundation Models
**Research / Side Project** · [Full Write-Up]({{ '/blog/dynscm-forecasting/' | relative_url }})

Designing a **dynamic structural causal model** as a synthetic prior for pretraining tabular foundation models on forecasting tasks. Honest negative results on what makes synthetic task geometry actually useful.

`causal-inference` `tabular-foundation-models` `forecasting` `synthetic-data`

---

## Industry

### LLM-Based Contract Compliance System
**Saint-Gobain, 2024–Present** · [Full Write-Up]({{ '/blog/llm-contract-compliance/' | relative_url }})

RAG-based system for evidence-grounded checklist verification. Improved decision stability to **<1% variance** using **hybrid similarity ranking** (semantic + TF-IDF). Adopted by 3 legal teams.

`LLM` `RAG` `vector-DB` `FastAPI` `Azure`

---

### Bayesian Predictive Maintenance
**Saint-Gobain, 2024–Present** · [Full Write-Up]({{ '/blog/bayesian-maintenance/' | relative_url }})

Failure-risk estimation using **Poisson/Cox-style event intensities** in **Pyro**. Includes sensor-driven root-cause analysis across 3 sites and 5 lines.

`Bayesian` `Pyro` `predictive-maintenance` `RCA`

---

### High-Precision Customer Segmentation
**Saint-Gobain, 2021** · [Case Study]({{ '/projects/segmentation-targeting/' | relative_url }})

Unsupervised behavioral discovery using **Laplacian Score** and **BIRCH clustering**. Increased click-through rate by **40%** and reduced runtime by **85%**.

`clustering` `feature-selection` `UMAP` `targeting`

---

### Time-Series Representation Learning
**EDF Lab & L'Oréal, 2019–2020** · [Case Study]({{ '/projects/time-series-representation/' | relative_url }})

Deep representation learning (**VAE, CAE, ConvLSTM**) for smart-meter curves and supply-chain forecasting. Reduced training time by **30x** while maintaining 94% accuracy.

`deep-learning` `time-series` `representation-learning` `PyTorch`

---

## Technical Notes & Theory
**arXiv / Technical Reports**

*   **Random Walks in Random Environments**: Recurrence, localization, and electrical-network analogies. [arXiv:2407.04758](https://arxiv.org/abs/2407.04758)
*   **Meta-Learning & Representation Learning**: Formalizing structure-sharing for data-scarce adaptation. [arXiv:2407.04189](https://arxiv.org/abs/2407.04189)

`stochastic-processes` `meta-learning` `theory`
