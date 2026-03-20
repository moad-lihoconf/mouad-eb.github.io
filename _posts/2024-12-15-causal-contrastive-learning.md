---
title: "Causal Contrastive Learning for Counterfactual Regression Over Time"
slug: "causal-contrastive-learning-counterfactual-regression-over-time"
date: "2026-03-20"
permalink: /blog/causal-contrastive-learning/
tags:
  - causal inference
  - representation learning
  - time series
  - self-supervised learning
  - healthcare AI
  - NeurIPS 2024
summary: "A research story about how I used contrastive learning and information-theoretic regularization to improve long-horizon counterfactual prediction under time-varying confounding."
featured: true
---


# Causal Contrastive Learning for Counterfactual Regression Over Time

*How can we predict what would have happened under a different sequence of future decisions, when treatments, covariates, and outcomes all evolve together over time?*

This question sits at the core of many real-world decision problems. In healthcare, for example, clinicians do not only want to predict a patient’s future state under the treatment that was actually given. They want to estimate what might happen under **alternative treatment plans**: a different drug, a delayed intervention, or a different sequence of actions over the next few days.

That is a hard problem.

In longitudinal observational data, treatment decisions are rarely random. They depend on a patient’s evolving condition, and that same condition also affects future outcomes. This creates **time-varying confounding**. On top of that, long-range temporal dependencies matter: early events can influence what happens much later, and models that focus too much on the local past may miss the broader structure of the process.

In this project, I asked a simple question:

> Can we build a model for long-horizon counterfactual forecasting that is both **causally grounded** and **computationally efficient**, without relying on large transformer architectures?

My answer was **Causal CPC**, a method that combines recurrent sequence modeling with ideas from **Contrastive Predictive Coding (CPC)** and **Information Maximization (InfoMax)**.

---

## The problem

The goal is to estimate a future potential outcome of the form

$$
\mathbb{E}\big[Y_{t+\tau}(\omega_{t+1:t+\tau}) \mid H_{t+1}\big],
$$

where:

- $H_{t+1}$ is the full observed history up to time $t+1$,
- $\omega_{t+1:t+\tau}$ is a hypothetical future treatment sequence,
- and $Y_{t+\tau}(\omega_{t+1:t+\tau})$ is the outcome we would observe under that sequence.

Under standard assumptions such as **consistency**, **sequential ignorability**, and **overlap**, this quantity is identifiable from observational data. But identification on paper does not automatically give a good learning algorithm in practice.

The practical challenges are severe:

1. **Time-dependent confounding**: past treatment changes future covariates, which then influence later treatment and later outcomes.
2. **Selection bias**: treatment groups are not balanced.
3. **Long-term dependencies**: useful signal may be distributed across long temporal windows.
4. **Inference cost**: evaluating many possible treatment trajectories becomes expensive as the horizon grows.

Most prior work attacked parts of this problem with recurrent models, weighting methods, adversarial balancing, or transformers. But I felt there was still room for a model that was simpler, faster, and more deliberate about *what kind of representation* it should learn.

---

## The central idea

Instead of treating counterfactual forecasting as purely a supervised prediction problem, I framed it as a **representation learning** problem.

A useful historical representation should satisfy three properties:

### 1. It should remember what matters for the future
A model that only encodes local patterns may perform well at short horizons but degrade when predictions must extend further in time.

### 2. It should preserve confounding information
In causal inference, throwing away “difficult” information can be dangerous. If the learned representation forgets variables that matter for treatment assignment and outcomes, counterfactual estimates can become biased.

### 3. It should make treatment bias easier to handle
The decoder should operate on a representation that is informative for outcomes but as balanced as possible across treatment regimes.

Causal CPC was designed around these three principles.

---

## What I built

The model has two stages.

## Stage 1 — Learn a good history representation

I encode the history using a lightweight recurrent architecture:

- local inputs are first embedded into latent features,
- then a **GRU** summarizes the sequence into a context vector $C_t$.

But instead of training this encoder only through outcome prediction, I pretrain it with two self-supervised objectives.

### A. Contrastive Predictive Coding (CPC)

The encoder is trained to make the current context predictive of future local features at several horizons.

Intuitively, this forces the model to answer the question:

> “From what I have seen so far, can I recognize what the future of this same individual should look like?”

This is done with an **InfoNCE** contrastive objective. The context representation must match the correct future and distinguish it from futures coming from other individuals in the batch.

That design gives the encoder an explicit incentive to model **long-range temporal structure**, rather than only fitting one-step-ahead targets.

### B. InfoMax for reconstructability

There is a subtle causal issue here.

In many causal models, the identification assumptions are stated on the **original history space**. But once you replace the history with a learned representation, those guarantees are no longer automatic. If the representation discards relevant confounding information, the causal story breaks.

To address this, I introduced an **InfoMax-style regularization**. Instead of directly reconstructing the full high-dimensional history with a heavy decoder, I split the history into two non-overlapping views and maximize mutual information between their learned representations.

The role of this objective is to push the representation toward being **reconstructable**: not necessarily invertible in a strict algebraic sense, but rich enough to preserve the relevant information carried by the original history.

This was, for me, one of the most important conceptual pieces of the work: I wanted the representation learner to be not only predictive, but also **causally respectful**.

---

## Stage 2 — Fine-tune for counterfactual prediction

After pretraining the encoder, I attach a decoder that autoregressively predicts future outcomes under a candidate future treatment sequence.

To reduce treatment-induced bias, I also learn a treatment prediction network and set up an adversarial game.

The idea is simple:

- the treatment network tries to predict treatment from the learned representation,
- the representation learner tries to make that harder,
- while still preserving information needed for accurate response prediction.

Instead of using a standard domain-confusion loss, I formulated this step with a **mutual-information upper bound**, using the **CLUB** estimator.

This gives a clean information-theoretic interpretation:

- if mutual information between representation and treatment is high, treatment assignment is still strongly encoded,
- if it is minimized, the representation becomes more balanced across treatment regimes.

One theoretical result of the paper shows that, at equilibrium, this adversarial game corresponds to exact balance of the representation distribution across treatments.

---

## Why I think this work is interesting

There are three reasons I find this project meaningful.

### 1. It connects causal inference and self-supervised learning in a nontrivial way
Contrastive learning is widely used in representation learning, but much less so in **counterfactual regression over time**. In this project, CPC was not added as a fashionable regularizer; it was chosen because the downstream task genuinely depends on **future-aware sequence representations**.

### 2. It treats representation quality as a causal issue, not just an optimization issue
The InfoMax component came from a causal concern: if representation learning discards confounding structure, the counterfactual task can fail for the wrong reason. I wanted to make representation preservation part of the modeling philosophy.

### 3. It shows that simple backbones can still be powerful
Much of the recent literature defaults to transformers for long-context modeling. Here, a **single-layer GRU**, combined with the right training objectives, was enough to achieve strong long-horizon performance while keeping inference efficient.

---

## Empirical results

I evaluated the method against strong baselines including **MSM**, **RMSN**, **CRN**, **G-Net**, and the **Causal Transformer**.

### Synthetic tumor-growth simulation
On a pharmacokinetic/pharmacodynamic cancer simulation with varying confounding strength, Causal CPC consistently showed its strongest advantage at **longer forecasting horizons**.

This pattern matters. The goal of the method was not merely to improve short-term interpolation, but to better model delayed and distributed temporal effects.

### Semi-synthetic MIMIC-III
On the semi-synthetic MIMIC-III benchmark, the method achieved strong performance across horizons and compared favorably to transformer-based baselines, especially as the prediction horizon increased.

For sequence length 100, the reported RMSE evolved from **0.32 at horizon 1** to **0.77 at horizon 10**, while the Causal Transformer rose from **0.42 to 0.88**, and G-Net and RMSN degraded more substantially at long horizons.

### Real MIMIC-III
True counterfactuals are unavailable in real clinical data, so evaluation there used factual forecasting along observed treatment paths. Even in this setting, the model remained competitive and outperformed baselines, especially at larger horizons.

### Ablation study
This was an important part of the paper.

When I removed either:

- the **CPC objective**,
- the **InfoMax objective**,
- or the **balancing mechanism**,

performance degraded.

That is exactly what I hoped to see: the gains were not coming from incidental implementation choices, but from the combined design of the method.

---

## Efficiency matters

One part of the project that I care about a lot is **efficiency**.

Counterfactual forecasting is not just about fitting a model once. In practice, one often wants to evaluate many future treatment plans per patient. If there are $K$ possible treatments over a horizon $\tau$, the number of possible trajectories grows like $K^\tau$.

That means inference speed matters.

In the experiments, Causal CPC had substantially lower prediction time than the transformer baseline while remaining highly competitive or superior in performance. This makes the method much more attractive in applied settings where repeated evaluation is needed.

For me, this is not a side note. It is part of the research contribution: a model should not only be theoretically motivated and accurate, but also *usable*.

---

## Limitations

I do not see this work as “the final model” for longitudinal causal forecasting.

Several limitations remain.

### It is stronger at long horizons than short ones
This is the main trade-off of the method. Because CPC emphasizes predictive information across future windows, the model is particularly good at capturing global temporal structure. That does not always translate into the best short-term performance.

### It still relies on standard observational assumptions
Like many methods in this area, the framework assumes **sequential ignorability**. When this assumption is violated, performance degrades, even though the model remained comparatively robust in the falsifiability study.

### Continuous treatment is not fully solved
The paper sketches how the framework could be extended to continuous treatments, but that is not yet the mature version of the method.

---

## What I personally contributed and learned

This project reflects several themes that I care deeply about as a researcher:

- bringing together **causal reasoning** and **modern representation learning**,
- thinking carefully about the relation between **identification assumptions** and **learned latent spaces**,
- and designing models that are not only accurate, but also **efficient and interpretable in their logic**.

It also strengthened my belief that many important advances do not come from using the biggest possible architecture. Sometimes they come from asking a sharper question:

> What kind of inductive bias does this causal problem actually need?

In this case, the answer was not “more layers” or “more attention.”
It was:

- better temporal representation learning,
- better preservation of confounding information,
- and better control of treatment leakage into the latent space.

---

## Takeaway

Causal CPC is my attempt to rethink long-horizon counterfactual forecasting through the lens of self-supervised sequence representation learning.

The main message of the work is:

> **Long-term counterfactual prediction can benefit substantially from contrastive objectives and information-theoretic regularization, even with a simple recurrent backbone.**

I see this project as a step toward a broader research direction: building causal machine learning systems whose representations are not only predictive, but also structurally aligned with the assumptions required for trustworthy decision support.

---

## Links

- **Paper:** *Causal Contrastive Learning for Counterfactual Regression Over Time*  
- **Venue:** NeurIPS 2024  
- **Seminar talk:** Causal Club, University of Pisa, March 2025

---
