---
layout: page
title: Causal Contrastive Learning for Counterfactual Regression
permalink: /projects/causal-contrastive-learning/
---

# Causal Contrastive Learning for Counterfactual Regression Over Time

**Published in NeurIPS 2024** | [Paper](https://openreview.net/forum?id=bKOZYBJE4Z) | [GitHub](https://github.com/moad-lihoconf/causal-cpc)

---

## The Challenge: Longitudinal Selection Bias

In longitudinal observational studies, treatment assignments are often confounded by time-varying factors. Standard counterfactual regression methods struggle with **long-term dependencies** and **cumulative selection bias** over many time steps.

## The Solution: Information-Theoretic Regularization

We proposed a novel framework that leverages **Contrastive Predictive Coding (CPC)** to regularize the representation space of sequential data.

### Key Technical Innovations:
1. **Causal CPC Objective**: We introduced a contrastive loss that specifically targets the extraction of features relevant to *future* counterfactual outcomes while remaining invariant to current treatment selection.
2. **Temporal Balancing**: An adaptive weighting mechanism to handle the trade-off between outcome prediction accuracy and treatment-invariant representation.
3. **Efficient Inference**: Unlike autoregressive models that require sequential sampling, our method allows for fast, parallelized counterfactual forecasting over long horizons.

## Results & Impact

- **Generalization Guarantees**: Established theoretical bounds on the counterfactual error using the $d_{\mathcal{H}\Delta\mathcal{H}}$ distance between representation distributions.
- **Empirical Performance**: Outperformed state-of-the-art baselines (CRN, G-Net) on the G-formula benchmark and intensive synthetic longitudinal datasets.
- **Robustness**: Demonstrated significant reduction in "drift" over long-term multi-step-ahead forecasts.

---

## Technical Stack
- **Frameworks**: PyTorch, PyTorch Lightning
- **Optimization**: Information-theoretic regularization (Mutual Information estimation)
- **Domain**: Longitudinal Data, Causal Inference, Sequence Modeling
