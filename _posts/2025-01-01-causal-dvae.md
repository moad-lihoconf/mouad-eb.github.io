---
title: "Causal Dynamic Variational Autoencoder for Counterfactual Regression in Longitudinal Data"
slug: "causal-dynamic-variational-autoencoder-counterfactual-regression-longitudinal-data"
date: "2026-03-20"
permalink: /blog/causal-dvae/
tags:
  - causal inference
  - variational inference
  - time series
  - representation learning
  - healthcare AI
  - TMLR 2025
summary: "A research story about learning latent substitutes for unobserved adjustment variables in longitudinal causal inference, with theoretical guarantees and strong empirical gains over counterfactual baselines."
featured: true
---


# Causal Dynamic Variational Autoencoder for Counterfactual Regression in Longitudinal Data

*What should we do when treatment effects vary across individuals because of important baseline factors that were never measured?*

This project started from a frustration I had with a common simplification in longitudinal causal inference.

A large part of the literature focuses on **time-varying confounding**: treatments depend on evolving patient history, and that same history affects future outcomes. This is indeed one of the central difficulties of causal inference over time.

But in many real applications, that is not the whole story.

Even when confounding is properly handled, treatment response can still vary because of **unobserved baseline characteristics**: genetic predispositions, latent subtypes, environmental exposures, behavioral profiles, or other stable sources of heterogeneity. These variables may not determine treatment assignment, yet they can strongly shape outcomes and modify treatment response.

That distinction matters.

If such variables affect outcomes but not treatment assignment, they are **not confounders** in the usual sense. Still, ignoring them leads to a less informative and less personalized notion of treatment effect. Instead of estimating treatment response at the right level of heterogeneity, we average over hidden subpopulations.

This is the problem I wanted to address with **CDVAE**: a principled way to infer latent substitutes for these missing static variables and use them to improve individualized treatment effect estimation in longitudinal data.

---

## The problem

We observe, for each individual, a trajectory of:

- time-varying covariates $X_t$,
- treatments $W_t$,
- outcomes $Y_t$,
- and the history $H_t = [X_{\le t}, W_{<t}, Y_{<t}]$.

Under standard assumptions such as **consistency**, **sequential ignorability**, and **overlap**, one can identify the usual conditional treatment effect

$$
\tau_t(h_t) = \mathbb{E}[Y_t(1) - Y_t(0) \mid H_t = h_t].
$$

But suppose there also exists an **unobserved static variable** $U$ that influences the outcome process and may modify treatment effects.

Then the more refined quantity of interest is

$$
\tau_t(h_t, u) = \mathbb{E}[Y_t(1) - Y_t(0) \mid H_t = h_t, U = u].
$$

I call this the **Augmented Conditional Average Treatment Effect (ACATE)**.

The challenge is immediate:

> If $U$ is not observed, can we still recover a useful latent substitute $Z$ such that conditioning on $Z$ plays the same practical role as conditioning on $U$?

This is not the same problem as deconfounding.

I was not trying to infer hidden variables that affect **both** treatment and outcome. Instead, I focused on a different causal regime: hidden baseline variables that affect the **response sequence** and the **heterogeneity of treatment effects**, but not treatment assignment itself.

That shift in viewpoint is, in my opinion, one of the most interesting parts of the project.

---

## The central idea

The core idea of CDVAE is to treat the missing adjustment variables as a **latent variable** in a sequential probabilistic model.

The model learns a stochastic representation $Z$ from the full longitudinal trajectory and uses it to predict outcomes together with the observed history and treatment.

But this is not just a standard latent-variable model bolted onto a causal problem.

I wanted to answer a deeper question:

> **When does a learned latent representation actually count as a causally valid substitute for the missing adjustment variables?**

That question led to the main theoretical condition in the paper.

### A conditional Markov assumption on the response process

I introduced a **Conditional Markov Model of order $p$**, written $Z \sim \mathrm{CMM}(p)$, under which the response sequence factorizes as

$$
p_\theta(y_{\le T}, z \mid x_{\le T}, \omega_{\le T})
=
p(z) \prod_{t=1}^T p_\theta(y_t \mid y_{t-1:t-p}, x_{\le t}, \omega_{\le t}, z).
$$

Intuitively, this means that once we condition on a sufficiently rich latent variable $Z$ and a finite window of recent history, the distant past should no longer carry extra information about the current response.

This assumption does two jobs at once:

1. it gives the latent variable a role consistent with a **static parentless adjustment variable**,
2. and it becomes the key condition under which the learned substitute is **causally meaningful**.

From this, the paper proves that treatment ignorability still holds when we augment the observed history with $Z$, and therefore ACATEs conditioned on $Z$ are identifiable.

That was the theoretical heart of the work.

---

## What I built

CDVAE combines three layers of design:

1. a **dynamic variational autoencoder** for longitudinal responses,
2. a **causal weighting and balancing strategy** to handle selection bias,
3. and a **structured latent prior** to capture hidden population structure.

### 1. A sequential latent-variable model

The generative side models the response trajectory conditionally on covariates, treatment history, and a latent substitute $Z$.

The encoder infers a posterior distribution

$$
q_\phi(z \mid D_T), \qquad D_T = (Y_{\le T}, X_{\le T}, W_{\le T}),
$$

from the whole observed trajectory.

In practice, the model uses GRU-based encoders and decoders, so it remains relatively lightweight while still operating on long sequences.

### 2. Weighting for causal validity in observational data

A purely generative sequential model is not enough for counterfactual reasoning, because observational data suffer from treatment-selection bias.

To address this, I formulated the learning objective as a **weighted ELBO**, where the reconstruction term is importance-weighted using propensity-based weights. The goal is to align the effective treated and control distributions more closely and connect the variational objective to the estimation error of treatment effects.

This was important to me conceptually: I did not want the causal component to be an afterthought. The generative model and the counterfactual objective needed to be tied together mathematically.

### 3. A Gaussian mixture prior for hidden population structure

If the missing adjustment variables correspond to latent subgroups in the population, a unimodal Gaussian prior is often too crude.

So I used a **Gaussian mixture prior** over $Z$:

$$
p(z) = \sum_{c=1}^K \pi_c \, \mathcal{N}(z \mid \mu_c, \Sigma_c).
$$

This gives the model a natural way to represent latent clusters or population structure. In healthcare language, one can think of this as allowing different hidden patient types to occupy different regions of latent space.

I liked this part of the design because it connects causal heterogeneity to an interpretable geometric story: the latent space is not just a compressed code, but a place where hidden response strata can emerge.

---

## A contribution I particularly care about: the near-deterministic regime

There is a subtle problem with stochastic latent substitutes.

If the latent representation is random, then for the same patient trajectory one could in principle sample many different $z$ values and obtain slightly different individualized treatment effects. In practice, people often solve this informally by using the posterior mean. But that move is not always theoretically justified.

I wanted to make that issue explicit.

This led to one of the more original aspects of the paper: studying **dynamic VAEs in the near-deterministic regime**.

The decoder is modeled with Gaussian noise of variance $\sigma^2$, and the analysis studies the behavior as

$$
\sigma \to 0^+.
$$

In that regime, the posterior over the substitute becomes less diffuse, and the paper proves a consistency result showing that the estimated causal effect becomes asymptotically insensitive to the specific realization of the latent variable. Informally:

> as the model becomes near-deterministic, using a sampled latent substitute or using its posterior mean leads to the same treatment effect estimate.

I find this contribution particularly satisfying because it addresses a real gap between theory and practice in latent-variable causal models. In several deconfounder-style approaches, the theory quietly assumes deterministic latent substitutes while the actual model remains stochastic. Here, I tried to close that gap rather than ignore it.

---

## Why I think this work is interesting

There are several reasons I see CDVAE as more than just “another deep causal model.”

### 1. It separates hidden adjustment from hidden confounding

A lot of causal ML work treats missing variables through the lens of confounding. But many important missing variables are better understood as **effect modifiers** or **outcome-relevant adjustment variables**.

That distinction changes both the modeling problem and the identifiability story.

### 2. It asks what makes a latent variable causally valid

The paper is not content with saying “we learn a representation and it seems useful.” It asks under what assumptions the learned representation can play the role we want it to play.

For me, that is the difference between a convenient trick and a real causal modeling contribution.

### 3. It connects variational inference to treatment effect generalization

The training objective is not only a variational likelihood. It is connected to a **generalization bound for weighted PEHE**, which makes explicit how weighted factual risk, representation balance, and decoder variance influence treatment effect estimation error.

That bridge between latent-variable modeling and causal generalization theory was one of the main conceptual goals of the project.

### 4. It treats hidden population structure as something worth modeling directly

The GMM prior is not cosmetic. It reflects the idea that unobserved heterogeneity may come from real subgroup structure, and that the latent space should be flexible enough to capture it.

---

## Empirical story

I evaluated CDVAE on both **synthetic longitudinal data** and **semi-synthetic MIMIC-III data**.

### Synthetic data

The synthetic experiment was built to control the contribution of the hidden adjustment variables to the treated outcome trajectory.

As this hidden heterogeneity becomes stronger, standard baselines degrade substantially. CDVAE, by contrast, shows a much slower increase in error and remains the strongest model across configurations.

An especially interesting part of the study was the **substitute augmentation experiment**.

I took strong longitudinal causal baselines such as:

- RMSN,
- CRN,
- G-Net,
- Causal Transformer,
- and Causal CPC,

and evaluated them in three modes:

1. **base**: no hidden adjustment information,
2. **substitute**: augmented with the latent substitute learned by CDVAE,
3. **oracle**: augmented with the true adjustment variables.

This is one of my favorite empirical results in the paper.

Why? Because it shows that CDVAE is not only a competitive standalone model. It also learns a substitute representation that other causal models can use. In many settings, providing the CDVAE substitute moved baselines much closer to their oracle performance.

That is a strong sign that the latent variable is capturing something structurally meaningful.

### Semi-synthetic MIMIC-III

On MIMIC-III, the task is to estimate treatment effects of vasopressor interventions on blood pressure trajectories under a semi-synthetic construction built from real ICU covariates.

Again, CDVAE achieved the best PEHE among all compared methods. The same pattern appeared:

- base models performed worst,
- substitute-augmented baselines improved substantially,
- oracle versions remained slightly stronger in some cases,
- and CDVAE itself was the best overall performer.

To me, this supports the main claim of the work: learning a good latent substitute for hidden outcome heterogeneity is genuinely useful for longitudinal causal inference.

---

## Ablations and robustness

I put a lot of value on ablation studies, especially for models that combine several ingredients.

The paper removes, one by one:

- the IPM regularization,
- the posterior distribution-matching term,
- the weighting strategy,
- and the learnable variance that enables the near-deterministic regime.

The full model consistently performs best.

One result I found especially telling is that fixing the decoder variance at $\sigma = 1$ hurts performance noticeably. That supports the importance of letting the model move toward the near-deterministic regime rather than treating decoder variance as a secondary technical detail.

The paper also studies:

- sensitivity to violations of sequential ignorability,
- robustness to the number of mixture components in the prior,
- whether the learned substitute better captures the true latent cluster structure,
- and posterior predictive checks for the conditional response model.

These are all important because a model like this can otherwise be too easy to oversell. I wanted the paper to show not only where CDVAE works, but also what its assumptions buy us and where failure modes can appear.

---

## Limitations

I do not see CDVAE as a universal solution.

Several limitations are important.

### It still relies on sequential ignorability

The framework assumes that confounding is handled by the observed history. If truly unobserved confounders remain, identifiability can fail.

### The hidden variables are assumed static

CDVAE is designed for **time-invariant** missing adjustment variables. If the true latent effect modifiers evolve over time, the modeling assumptions become misspecified.

### The theory relies on finite conditional memory

The main identifiability argument uses a finite-order conditional Markov assumption. If the response process has effectively infinite memory, the theoretical picture weakens.

### Heavy-tailed outcomes or regime changes can be problematic

These cases are discussed explicitly in the paper as possible failure modes. I think it is important to state them openly because latent-variable causal models can otherwise sound more universal than they really are.

---

## What I personally contributed and learned

This project reflects several themes that matter deeply to me as a researcher.

First, I care a lot about **causal distinctions that are easy to blur**. The difference between hidden confounders and hidden adjustment variables is not just a matter of vocabulary; it changes the right mathematical question.

Second, I am interested in the boundary between **identification theory** and **representation learning**. If a paper introduces a latent variable for a causal task, I want to know what makes that latent variable legitimate beyond empirical usefulness.

Third, I like models that create a two-way dialogue between theory and practice. In CDVAE, the probabilistic model suggested the causal question, and the causal question forced a more careful treatment of the latent-variable model.

More broadly, the work strengthened my conviction that longitudinal causal inference still has many underexplored opportunities at the intersection of:

- latent-variable modeling,
- population structure,
- representation learning,
- and individualized treatment effect estimation.

---

## Takeaway

CDVAE is my attempt to bring latent-variable modeling into longitudinal causal inference in a way that is both **mathematically principled** and **empirically useful**.

The main message of the work is:

> **Missing baseline variables do not need to be confounders to matter for causal effect estimation. If they shape outcome heterogeneity, learning valid latent substitutes for them can substantially improve individualized treatment effect estimation over time.**

I see this project as part of a broader research direction: building causal ML methods whose latent representations are not only predictive, but also aligned with the precise causal role that missing variables play.

---

## Links

- **Paper:** *Causal Dynamic Variational Autoencoder for Counterfactual Regression in Longitudinal Data*
- **Venue:** Transactions on Machine Learning Research (TMLR), 2025

---
