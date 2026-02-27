---
layout: page
title: Non-Linear Mixed-Effect Causal Modeling
permalink: /projects/mixed-effect-causal/
---

# Non-Linear Mixed-Effect Causal Modeling for Sequential Data

**Presented at JdS 2023 & Causal@UAI 2024** | [Paper (JdS)](https://jds2023.sciencesconf.org/resource/page/id/19) | [Poster (UAI)](https://openreview.net/forum?id=dXwT6Dyo6z)

---

## Conceptual Bridge

This research bridges the gap between causal inference and unobserved heterogeneity in mixed-effects panel models. We connect unobserved risk factors with random effects, leveraging deep non-linear mixed-effects (NLME) modeling to estimate individual treatment effects (ITE) over time.

## The Problem: Unobserved Covariates & Transparency

Estimating causal effects in longitudinal data often suffers from **unobserved risk factors** (confounders) that correlate with treatment paths and outcomes. Furthermore, learned latent factors in deep models often lack transparency.

## Technical Solutions

### 1. Deep Non-Linear Mixed-Effects (NLME)
- **Random Effects as Confounders**: Formalized the interpretation of random effects as surrogates for unobserved time-invariant confounders.
- **Deep Parameterization**: Used neural networks to parameterize the non-linear transition functions and outcome maps.
- **Estimation**: Developed an efficient E-M algorithm variant to estimate parameters and infer subject-specific random effects simultaneously.

### 2. Transparent Causal representation learning (UAI 2024)
To improve interpretability, we introduced a link between latent variables and observed features:
- **Structural Sparsity**: Learned transparent causal factors via structural sparsity in generative functions.
- **Sparse Subspace Clustering**: Applied to **decoder Jacobians** to trace and visualize how specific latent dimensions influence observed outcome features.

## Application to Commercial Targeting

The model was applied to transactional customer data at Saint-Gobain to estimate the impact of commercial actions over multi-week horizons. By capturing evolving customer state and heterogeneity, we provided personalized action sequences for over **100 sales engineers**.

---

## Technical Keywords
`causal-inference` `longitudinal-data` `mixed-effects` `sparse-subspace-clustering` `latent-variables` `decoder-jacobians`
