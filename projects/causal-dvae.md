---
layout: page
title: Causal Dynamic VAE for Counterfactual Regression
permalink: /projects/causal-dvae/
---

# Causal Dynamic Variational Autoencoder (CDVAE) for Longitudinal Treatment Effects

**Published in TMLR 2025** | [Paper](https://openreview.net/forum?id=atf9q49DeF) | [GitHub](https://github.com/moad-lihoconf/cdvae)

---

## Overview

The **Causal Dynamic Variational Autoencoder (CDVAE)** is designed to infer unobserved risk factors that drive both outcome sequences and treatment assignments in panel data.

## Technical Breakdown

### 1. Latent-Variable Identifiability
A core contribution of this work is proving the **identifiability** of the latent causal factors under temporal structure. We leverage the dynamics of the outcome sequence to disentangle the time-varying latent state from static subject-specific heterogeneity.

### 2. Architecture: Recurrent Variational Inference
- **Encoder**: A bidirectional LSTM-based inference network that maps historical outcomes and treatments to a latent distribution.
- **Decoder**: A conditional generative network that predicts future outcomes based on the latent state and proposed treatment sequences.
- **Probabilistic Calibration**: Uses Importance Weighted Autoencoders (IWAE) to improve the tightness of the ELBO in sequential settings.

### 3. Generalization Bounds for ITE
We established the first generalization bounds for Individual Treatment Effect (ITE) estimation in the context of dynamic latent-variable models, providing a theoretical foundation for the stability of CDVAE forecasts.

## Key Outcomes

- **State-of-the-art Accuracy**: Significant error reduction in multi-step counterfactual prediction compared to non-generative sequence models.
- **Uncertainty Quantification**: The probabilistic nature of the model allows for natural confidence intervals around counterfactual trajectories, critical for high-stakes decision-making.
- **Synthetic & Real Data**: Validated on large-scale synthetic benchmarks and applied to longitudinal customer transaction data.

---

## Technical Stack
- **Tools**: PyTorch, Pyro, SciPy
- **Techniques**: Variational Inference, Identifiability Theory, Recurrent Neural Networks
