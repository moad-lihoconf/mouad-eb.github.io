---
layout: post
title: "Bayesian Predictive Maintenance with Pyro"
date: 2025-01-03
categories: [industry, bayesian]
tags: [Bayesian, Pyro, predictive-maintenance, calibration]
---

# Bayesian Predictive Maintenance with Pyro

This post covers the design of a Bayesian failure-risk model for manufacturing equipment, built to support maintenance triage decisions.

---

## Problem

Manufacturing lines experience unplanned stops. The maintenance team needs to:

1. **Predict** which assets are likely to fail soon
2. **Prioritize** inspections when resources are limited
3. **Understand uncertainty** — a 70% failure probability with high confidence is different from 70% with wide uncertainty

Point predictions from classical ML don't give this last piece.

---

## Approach: Bayesian Failure Models

We model failure risk as a posterior distribution, not a point estimate. This enables:

- **Uncertainty quantification**: Know when the model is confident vs. uncertain
- **Risk-aware triage**: Prioritize assets with high expected risk _and_ low uncertainty
- **Calibration**: Check that predicted probabilities match observed failure rates

---

## Model Structure

### Data

For each asset and time window:
- **Operational features**: sensor readings, usage hours, load patterns
- **Historical events**: past stops, maintenance logs, scrap rates
- **Target**: binary indicator of failure in the next N days

### Model

We use a Bayesian logistic regression with hierarchical priors:

$$
\text{logit}(p_i) = \alpha_{site[i]} + \beta^T x_i
$$

where:
- $\alpha_{site}$ is a site-level random effect (shared across assets at the same site)
- $\beta$ are feature coefficients
- Both have weakly informative priors

This captures site-level variation (different equipment, operators, conditions) while pooling information across sites.

---

## Implementation in Pyro

```python
def model(X, site_idx, y=None):
    # Priors
    alpha_mean = pyro.sample("alpha_mean", dist.Normal(0, 1))
    alpha_std = pyro.sample("alpha_std", dist.HalfNormal(1))
    
    with pyro.plate("sites", n_sites):
        alpha = pyro.sample("alpha", dist.Normal(alpha_mean, alpha_std))
    
    beta = pyro.sample("beta", dist.Normal(0, 1).expand([n_features]))
    
    # Likelihood
    logits = alpha[site_idx] + X @ beta
    with pyro.plate("data", len(X)):
        pyro.sample("obs", dist.Bernoulli(logits=logits), obs=y)
```

Training uses stochastic variational inference (SVI) with an autoguide.

---

## Posterior Inference

After training, we draw samples from the posterior to get:

- **Predicted probability**: mean of posterior predictive
- **Uncertainty**: standard deviation or credible intervals
- **Site effects**: posterior distribution of $\alpha_{site}$

---

## Calibration

We evaluate calibration on held-out data:

| Predicted Probability Bin | Observed Failure Rate |
|---------------------------|----------------------|
| 0.0–0.1 | 0.05 |
| 0.1–0.2 | 0.14 |
| 0.2–0.3 | 0.26 |
| ... | ... |

Good calibration means predicted probabilities are reliable. We use reliability diagrams and Brier score decomposition.

---

## Triage Strategy

Assets are ranked by:

$$
\text{priority} = \mathbb{E}[p] + \lambda \cdot \text{Var}[p]
$$

This balances expected risk with uncertainty. The parameter $\lambda$ lets the maintenance team choose how conservative to be.

---

## Results

Deployment across 3 sites and 5 production lines:
- 98% cross-warehouse record matching after data harmonization
- Calibration error (ECE) < 0.05 on backtests
- Maintenance team uses posterior credible intervals to decide inspection urgency

---

## Stack

| Component | Technology |
|-----------|------------|
| Probabilistic programming | Pyro (PyTorch) |
| Data pipeline | SQL (Snowflake), Python |
| Experiment tracking | MLflow |
