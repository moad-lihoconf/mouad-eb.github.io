---
layout: page
title: Non-Linear Mixed-Effect Causal Modeling
permalink: /projects/mixed-effect-causal/
---

# Non-Linear Mixed-Effect Causal Modeling for Sequential Data

**Presented at the French Statistical Society (JdS) 2023** | [Paper](https://jds2023.sciencesconf.org/resource/page/id/19)

---

## Conceptual Bridge

This research bridges the gap between two traditionally separate fields:
1. **Causal Inference**: Focusing on Individual Treatment Effect (ITE) and unobserved confounding.
2. **Mixed-Effect Modeling**: Traditionally used in panel data to handle unobserved heterogeneity via random effects.

## The Problem: Unobserved Covariates in Sequences

Estimating causal effects in longitudinal data often suffers from **unobserved risk factors** that correlate with both treatment paths and outcomes.

## Technical Solution

We leveraged **Deep Non-Linear Mixed-Effects Models (NLME)** to capture these unobserved factors.

### Key Contributions:
- **Random Effects as Confounders**: Formalized the interpretation of random effects in NLME as surrogates for unobserved time-invariant confounders.
- **Deep Parameterization**: Used neural networks to parameterize the non-linear transition functions and outcome maps within the mixed-effects framework.
- **Estimation**: Developed an efficient E-M algorithm variant to estimate parameters and infer subject-specific random effects simultaneously.

## Application to Marketing Targeting

The model was applied to transactional customer data at Saint-Gobain to estimate the impact of commercial actions over multi-step horizons, allowing for personalized action sequences that account for high-cardinality customer heterogeneity.

---

## Technical Keywords
`causal-inference` `longitudinal-data` `mixed-effects` `variational-EM` `latent-variables`
