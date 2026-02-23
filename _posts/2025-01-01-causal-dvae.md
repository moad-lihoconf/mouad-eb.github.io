---
layout: post
title: "Causal Dynamic VAE for Longitudinal Treatment Effects"
date: 2025-01-01
categories: [research, causal-inference]
tags: [causal-inference, variational-inference, identifiability, TMLR]
---

# Causal Dynamic VAE for Counterfactual Regression in Longitudinal Data

This post covers the technical details of our TMLR 2025 paper on using variational autoencoders for individual treatment effect estimation.

---

## Problem

In observational longitudinal data, outcomes depend on:

1. **Observed covariates** — patient history, demographics
2. **Applied treatments** — interventions over time
3. **Unobserved risk factors** — latent heterogeneity that affects both treatment selection and outcomes

Standard methods assume all confounders are observed. When they're not, treatment effect estimates are biased.

---

## Approach: CDVAE

We introduce the **Causal Dynamic Variational Autoencoder (CDVAE)**, which:

1. **Infers latent risk factors** — the encoder learns time-varying latent states capturing unobserved heterogeneity
2. **Models outcome dynamics** — the decoder predicts outcomes conditional on latent state, treatment, and history
3. **Supports counterfactual queries** — by intervening on the treatment variable while holding latent state fixed

---

## Model Structure

### Generative Model

At each time step $t$:

$$
z_t \sim p(z_t \mid z_{t-1}, x_{t-1}, a_{t-1})
$$

$$
y_t \sim p(y_t \mid z_t, a_t, x_t)
$$

where $z_t$ is the latent state, $x_t$ are covariates, $a_t$ is treatment, and $y_t$ is the outcome.

### Inference Model

The encoder approximates the posterior:

$$
q(z_t \mid z_{t-1}, x_{\leq t}, a_{<t}, y_{<t})
$$

We use amortized variational inference, training with the ELBO.

---

## Identifiability

A key contribution is proving **identifiability** of the latent factors under mild conditions:

1. The generative model satisfies a conditional independence structure
2. The latent transition has sufficient variability
3. There's enough diversity in treatment assignments

Under these conditions, the learned $z_t$ corresponds (up to reparametrization) to the true unobserved factors.

This means the counterfactual predictions are grounded in a well-defined causal model, not just a black-box fit.

---

## Generalization Bounds

We derive bounds on counterfactual prediction error in terms of:

- Training sample size
- Latent dimension
- Distributional shift between factual and counterfactual regimes

These bounds guide model selection and clarify when the method should work.

---

## Results

On semi-synthetic and real-world datasets:

| Dataset | CDVAE | RNN Baseline | Improvement |
|---------|-------|--------------|-------------|
| Tumor Growth | 0.42 | 0.58 | -28% |
| MIMIC-III | 0.31 | 0.39 | -21% |

The method performs especially well when unobserved confounding is present.

---

## Implementation

- **Framework**: PyTorch, Pyro for variational inference
- **Training**: standard VAE training with KL annealing
- **Hyperparameters**: latent dimension, encoder/decoder depth, KL weight schedule

---

## Paper

M. El Bouchattaoui, M. Tami, B. Lepetit, P.-H. Cournède.  
*Causal Dynamic Variational Autoencoder for Counterfactual Regression in Longitudinal Data.*  
TMLR 2025. [OpenReview](https://openreview.net/forum?id=atf9q49DeF)
