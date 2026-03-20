---
title: "Random Effects in Longitudinal Data Through the Lens of Causal Inference"
slug: "random-effects-longitudinal-data-causal-inference"
date: "2025-09-15"
permalink: /blog/mixed-effect-causal/
tags:
  - causal inference
  - longitudinal data
  - mixed effects models
  - variational inference
  - treatment effect heterogeneity
  - research talks
summary: "A portfolio-style research note on how I reframed random effects in longitudinal models as latent causal adjustment variables, and how that viewpoint led toward a variational approach for individualized treatment-effect estimation over time."
featured: false
---


# Random Effects in Longitudinal Data Through the Lens of Causal Inference

Some research projects begin as a polished method. Others begin as a conceptual irritation.

This one started from a simple question:

> **What exactly are random effects doing, from a causal point of view, when we use them to model heterogeneous treatment response in longitudinal data?**

Classical mixed-effects models are extremely useful. They let us describe subject-level variation, absorb unobserved heterogeneity, and improve prediction when repeated measurements are available. But in many applications, especially in health or behavioral settings, we are not only interested in prediction. We want to estimate **individualized treatment effects over time**.

At that point, the usual statistical language of “random effects” starts to feel incomplete.

It tells us that some parameters vary across individuals. It does **not** immediately tell us how to think about those variations in causal terms: Are they confounders? Are they effect modifiers? Are they hidden baseline factors that influence the response process but not treatment assignment? And what changes once we adopt that interpretation explicitly?

This blog post describes that research idea: a causal reinterpretation of random effects in longitudinal models, and how that reinterpretation naturally leads to a latent-variable approach for counterfactual estimation.

---

## The starting point: treatment effects are heterogeneous for reasons we do not always observe

Imagine a longitudinal patient setting. At each time step, we observe:

- a treatment $W_t$,
- an outcome $Y_t$,
- observed covariates $X_t$,
- and a hidden baseline factor $U$.

A simple motivating example is:

- $Y_t$: frailty or health status,
- $W_t$: whether the patient receives vitamin D,
- $X_t$: observed socio-economic or clinical covariates,
- $U$: an unobserved baseline factor such as age, latent health subtype, or a stable physiological profile.

What matters here is that $U$ may influence the outcome process and also change **how the treatment works**, even if it does not determine treatment assignment itself.

This is the key conceptual move.

A lot of causal inference focuses on **confounders**, meaning variables that affect both treatment and outcome. But many important hidden variables are not confounders in that sense. Instead, they act as:

- **adjustment variables**, because they affect the response process,
- and sometimes **effect modifiers**, because they change the magnitude or shape of the treatment effect.

That distinction is subtle, but it matters a lot if the goal is to estimate treatment effects as precisely as possible at the individual level.

---

## Why the usual mixed-effects interpretation felt insufficient

In a classical nonlinear mixed-effects model, one may write something like

$$
\mathbb{E}[Y_{i,t} \mid W_{i,t}, X_{i,\le t}, Y_{i,<t}, \alpha_i^{(1)}, \alpha_i^{(2)}]
=
\gamma_1 Y_{i,t-1} + \gamma_2 Y_{i,t-2}
+
\Big(\beta_1 Y_{i,t-1} + (\beta_2 + \alpha_i^{(1)})X_{i,t} + \alpha_i^{(2)}\Big)W_{i,t}
+ \beta_3 X_{i,t}.
$$

Here, the individual-specific random effects $\alpha_i^{(1)}, \alpha_i^{(2)}$ capture heterogeneity across subjects.

From a purely statistical viewpoint, that is perfectly natural.

But causally, something more interesting is happening.

The treatment effect itself becomes

$$
\tau_{i,t}
=
\mathbb{E}[Y_{i,t} \mid W_{i,t}=1, \cdots] - \mathbb{E}[Y_{i,t} \mid W_{i,t}=0, \cdots]
=
\beta_1 Y_{i,t-1} + (\beta_2 + \alpha_i^{(1)})X_{i,t} + \alpha_i^{(2)}.
$$

So the random effects are not merely nuisance terms. They directly enter the **individual treatment effect**.

That suggests a different reading:

> perhaps the random effects are the statistical shadow of an unobserved causal variable $U_i$.

In other words, instead of treating $\alpha_i$ as free subject-level parameters, we can imagine that

$$
\alpha_i = \alpha(U_i),
$$

for some unknown mapping from a hidden baseline variable $U_i$ into the individual-specific response behavior.

This was the conceptual heart of the project.

---

## The causal question behind the model

Once we reinterpret random effects as functions of hidden baseline variables, the problem becomes a causal one.

Under binary treatment, with potential outcomes $Y_t(1)$ and $Y_t(0)$, the quantity of interest is no longer just

$$
\mathbb{E}[Y_t(1)-Y_t(0) \mid X_{\le t}, Y_{<t}],
$$

but rather the more refined quantity

$$
\tau_t
=
\mathbb{E}[Y_t(1)-Y_t(0)
\mid X_{\le t}=x_{\le t},\, Y_{<t}=y_{<t},\, U=u].
$$

This formulation says something important:

- observed history matters,
- but so does a hidden stable factor $U$,
- because $U$ can help explain why two individuals with similar observed histories still respond differently to the same intervention.

Under **sequential ignorability**, treatment assignment can still be handled using the observed history. But for precise individualized effect estimation, conditioning on observed history alone may not be enough if there remains hidden outcome-relevant heterogeneity.

That is exactly where the mixed-effects intuition becomes causally valuable.

---

## A distinction I care a lot about: confounders versus hidden adjustment variables

One of the main ideas I wanted to make explicit is that causal inference should not collapse all missing variables into the same bucket.

There are at least three different roles a hidden variable can play:

1. it can affect treatment and outcome, in which case it is an **unobserved confounder**,
2. it can affect the outcome but not treatment, in which case it is an **unobserved adjustment variable**,
3. and it can specifically alter the size or direction of the treatment effect, in which case it acts as an **effect modifier**.

The project focused on the second and third roles, not the first.

That is important because the goal was **not** to solve hidden confounding in full generality. The goal was to estimate treatment response more precisely by learning a representation of stable hidden heterogeneity that shapes the response dynamics.

This is, in my view, a cleaner and more targeted problem than many “latent deconfounding” formulations.

---

## The modeling move: turn the hidden adjustment variable into a latent representation

Once the problem is framed this way, a natural idea follows.

If $U$ is unobserved, then instead of estimating free random effects directly, we can try to **learn a latent representation** that plays the role of $U$.

The probabilistic model becomes

$$
p_\theta(y_{\le T}, u \mid x_{\le T}, w_{\le T})
=
\prod_{t=1}^T p_\theta(y_t \mid y_{<t}, x_{\le t}, w_{\le t}, u)\, p(u),
$$

together with an inference model

$$
q_\phi(u \mid y_{\le T}, x_{\le T}, w_{\le T}).
$$

This is the moment where the project leaves the language of classical mixed-effects models and enters the language of **latent-variable causal modeling**.

Instead of assigning each individual a pair of random coefficients, we infer a latent representation from the full observed trajectory and then condition treatment-effect estimation on that representation.

Conceptually, I liked this formulation because it did several things at once:

- it kept the longitudinal structure,
- it kept the causal question,
- it gave a role to hidden stable heterogeneity,
- and it opened the door to flexible neural parameterizations instead of rigid parametric random-effects assumptions.

---

## Why a variational model made sense here

To learn such a latent representation, I formulated the model through a variational objective.

The evidence lower bound takes the usual form

$$
\mathrm{ELBO}(\theta,\phi)
=
\sum_{t=1}^T
\mathbb{E}_{q_\phi(u \mid y_{\le T}, x_{\le T}, w_{\le T})}
\big[
\log p_\theta(y_t \mid y_{<t}, x_{\le t}, w_t, u)
\big]
-
D_{\mathrm{KL}}\big(q_\phi(u \mid y_{\le T}, x_{\le T}, w_{\le T}) \| p(u)\big).
$$

The point was not merely to compress trajectories into a latent code.

The point was to learn a latent representation that remains useful for a **counterfactual regression task**. So the outcome model was designed in a TARNet-style manner, where the latent variable informs potential outcome prediction under different treatment assignments.

In parallel, treatment assignment was modeled through a propensity network, allowing the learning objective to incorporate **propensity-based weighting**.

This produced a weighted training loss that combined three ingredients:

- trajectory reconstruction / response modeling,
- latent regularization through the KL term,
- and causal reweighting to better support counterfactual estimation in observational settings.

This is the part where the early research idea begins to look like the seed of what later became a fuller method.

---

## Architecture at a glance

The talk version of the project used a fairly intuitive architecture:

- an **RNN-based inference network** to construct a representation of the hidden variable from the full observed trajectory,
- an **RNN-based outcome model** to predict factual and counterfactual outcomes over time,
- and a **propensity network** to learn weights used in the counterfactual regression loss.

I like this architecture because it is structurally honest.

The different pieces correspond to distinct statistical roles:

- one network summarizes hidden heterogeneity,
- one models response dynamics,
- one corrects for treatment-selection bias.

That decomposition made the model easier to reason about, and it also made clear that the representation was meant to support a causal estimation task rather than exist as an abstract embedding with no defined role.

---

## A practical issue that turned out to matter: posterior collapse

Like many latent-variable models, this one faced the familiar risk of **posterior collapse**: the encoder learns a latent variable that the decoder more or less ignores.

That is especially problematic here, because if the latent variable is supposed to represent hidden treatment-effect heterogeneity, then a collapsed posterior means the very scientific object we care about has failed to enter the model meaningfully.

To mitigate this, I explored a **cyclical schedule** for the KL weight $\beta$, inspired by cyclical annealing strategies for VAEs.

This led to two variants:

- **CDVAE** with a standard KL weighting,
- **$\beta$-cyc-CDVAE** with a cyclically updated KL coefficient.

In the empirical results, the cyclic strategy was not a cosmetic training trick. It materially improved the balancing between prediction and latent usage.

That mattered because it revealed something deeper:

> in causal latent-variable modeling, training dynamics are part of the scientific story, not just an optimization footnote.

---

## Synthetic experiments: a controlled testbed for hidden heterogeneity

To test the idea, I used a synthetic longitudinal setup with:

- sequence length $T=35$,
- observed confounders $X_t \in \mathbb{R}^{100}$ following a VAR process,
- an unobserved adjustment variable $U \in \mathbb{R}^{100}$,
- and a treatment effect depending jointly on observed and hidden components.

The treatment effect was defined as

$$
\tau(X_t,U)
=
\exp\left(
\frac{1}{d_x}\sum_{j=1}^{d_x} X_{t,j}
+
\frac{1}{d_u}\sum_{j=1}^{d_u} U_j
\right).
$$

This choice made the task deliberately challenging: the treatment effect depends on both the observed history and hidden stable variation.

That is precisely the situation where the usual longitudinal baselines, even when strong, can struggle if they do not model the hidden structure explicitly.

---

## What the results suggested

The model was compared against several baselines, including:

- recurrent marginal structural models,
- counterfactual recurrent networks,
- and causal forest / double machine learning style baselines.

The most encouraging result was that the cyclic latent model achieved the strongest overall compromise between:

- treatment-effect estimation,
- response prediction,
- and stable training behavior.

A particularly informative observation from the training curves was the contrast between:

- **with cycling**: better balancing and healthier latent usage,
- **without cycling**: poorer balance and a clear sign that the training objective could settle into a less useful regime.

I appreciate this result because it says something broader than “our model wins a table.”

It says that once hidden adjustment variables are modeled through latent representations, the way the latent channel is kept active becomes central to whether the causal story survives training.

---

## Why I think this work matters conceptually

For me, this project was important less because of any individual architecture choice and more because of the conceptual reframing it introduced.

### 1. It gives random effects a causal interpretation

Rather than treating random effects as anonymous subject-specific perturbations, it asks what hidden causal structure they might be standing in for.

### 2. It separates “missing variables that bias treatment assignment” from “missing variables that refine treatment response” 

That distinction is often blurred in the literature, but it changes both the assumptions and the modeling strategy.

### 3. It connects classical statistics and modern representation learning

Mixed-effects models and latent-variable neural models are often presented as belonging to different methodological worlds. I see them instead as two ways of approaching the same underlying problem: stable subject-level heterogeneity in longitudinal response.

### 4. It treats individualized treatment effects as a representation-learning problem with causal structure

The latent representation is not learned for its own sake. It is learned because a missing variable with a well-defined causal role ought to be represented somehow if we want precise individualized inference.

---

## In retrospect: this was a bridge project

Looking back, I see this work as a **bridge** between two research cultures:

- the classical world of mixed-effects longitudinal modeling,
- and the newer world of causal representation learning.

It was also a bridge in my own trajectory.

This project helped me articulate an idea that would become increasingly important in my later work:

> **latent variables in causal models should not be introduced only because they improve predictive fit; they should correspond to a precise missing causal role.**

That principle shaped a lot of what came later.

In that sense, this was not just a standalone talk. It was an early formulation of a broader research direction.

---

## Limitations and open questions

The project also made several limitations very clear.

### The hidden variable was assumed static

This is suitable for baseline heterogeneity, but less appropriate when the missing structure evolves over time.

### The approach still relied on sequential ignorability

So it did not solve hidden confounding in full generality.

### Learning a good latent substitute is not only a modeling issue but also a training issue

The posterior-collapse problem made that impossible to ignore.

### The next natural step was obvious

If hidden baseline adjustment variables can be represented this way, then one wants:

- stronger theory,
- cleaner identification arguments,
- richer priors for latent population structure,
- and more systematic empirical validation.

Those questions directly motivated the more mature work that followed.

---

## Takeaway

This project asked a simple but, in my opinion, underappreciated question:

> **What do random effects mean when the real objective is causal effect estimation over time?**

My answer was to reinterpret them as the manifestation of hidden adjustment variables and effect modifiers, then learn their representation explicitly with a longitudinal latent-variable model.

That move created a conceptual bridge from classical mixed-effects thinking to causal representation learning, and it opened a path toward more principled individualized treatment-effect estimation in sequential settings.

---

## Links

- **Talk:** *A Non-Linear Mixed Effect Model for the Estimation of Causal Effects Through Time* (Journées de Statistique, 2023)
- **Talk:** *Random Effects in Longitudinal Data from the Perspective of Causal Inference* (Fondements Mathématiques de l’IA, 2024)


