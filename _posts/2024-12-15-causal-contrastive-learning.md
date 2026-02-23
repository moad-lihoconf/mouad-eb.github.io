---
layout: post
title: "Causal Contrastive Learning for Counterfactual Regression"
date: 2024-12-15
categories: [research, causal-inference]
tags: [causal-inference, contrastive-learning, time-series, NeurIPS]
---

# Causal Contrastive Learning for Counterfactual Regression Over Time

This post describes the technical approach behind our NeurIPS 2024 paper on counterfactual regression in longitudinal settings.

---

## Problem

Estimating individual treatment effects (ITE) from observational time-series data is difficult for two reasons:

1. **Selection bias** — treatment assignment depends on patient/customer history
2. **Long horizons** — effects unfold over many time steps, and errors compound

Standard approaches either ignore temporal structure or model it with recurrent networks, but struggle to balance bias correction with long-horizon prediction.

---

## Approach

We address both issues through a contrastive learning framework designed for counterfactual regression:

### Key Ideas

1. **Representation learning** — learn embeddings that are predictive of outcomes but invariant to treatment assignment (after conditioning on confounders)

2. **Contrastive objective** — instead of reconstruction, we use contrastive losses to:
   - Pull together representations of similar patient trajectories
   - Push apart trajectories that differ in ways unrelated to treatment

3. **Information-theoretic regularization** — bound the mutual information between learned representations and treatment indicators to reduce selection bias leakage

---

## Technical Details

### Model Architecture

The model has three components:

```
Past trajectory → Encoder → Latent state z_t
                              ↓
Treatment a_t → Decoder → Predicted outcome y_{t+1}
```

The encoder is a temporal model (Transformer or GRU) mapping history to a latent state. The decoder conditions on both `z_t` and `a_t` to predict outcomes.

### Contrastive Loss

For a batch of trajectories, we define positive pairs as:
- Same patient, nearby time steps
- Patients with similar covariate profiles

Negative pairs are drawn randomly. The loss encourages the encoder to capture outcome-relevant features.

### Bias Reduction

We add a regularization term penalizing mutual information between `z_t` and `a_t`. Intuitively, if the representation "leaks" information about which treatment was chosen, it may conflate treatment assignment patterns with treatment effects.

We approximate this with a variational bound using a discriminator network.

---

## Results

On semi-synthetic benchmarks (Tumor Growth, MIMIC-III), the method:

- Reduces counterfactual prediction error by 15–25% vs. RNN baselines
- Maintains accuracy over longer horizons (up to 20+ steps)
- Scales efficiently to large patient cohorts

---

## Implementation Notes

- **Framework**: PyTorch + PyTorch Lightning
- **Configuration**: Hydra for hyperparameters
- **Tracking**: MLflow for experiment logging
- **Key hyperparameters**: contrastive temperature, MI regularization weight, encoder depth

---

## Paper

M. El Bouchattaoui, M. Tami, B. Lepetit, P.-H. Cournède.  
*Causal Contrastive Learning for Counterfactual Regression Over Time.*  
NeurIPS 2024. [OpenReview](https://openreview.net/forum?id=bKOZYBJE4Z)
