---
layout: post
title: "Building a Long-Only Statistical Pairs Selection Framework"
slug: "pairs-trading-long-only"
date: "2025-05-01"
permalink: /blog/pairs-trading/
tags:
  - quantitative finance
  - statistical arbitrage
  - pairs trading
  - portfolio construction
  - convex optimization
summary: "How I turned classical pairs trading into a constrained long-only portfolio-construction problem using factor residualization, mean-reversion diagnostics, and convex optimization."
featured: false
---

# Building a Long-Only Statistical Pairs Selection Framework

---

## Why I started this project

Pairs trading is one of the most classical ideas in quantitative finance: find two assets that move together, wait for their relationship to diverge, and position for reversion. In its textbook form, the trade is elegant. In practice, however, many real portfolios cannot implement that textbook version directly.

The reason is simple: the canonical formulation assumes you can go long one asset and short the other, with few portfolio-level constraints. But many institutional mandates are **long-only**, benchmark-relative, capped at the name level, and tightly monitored for unintended factor exposures. In that world, “buy stock \(i\), short stock \(j\)” is no longer a primitive action. It becomes an **implementation problem**.

This personal project started from that tension. I wanted to ask a more practical question:

> Can we preserve the statistical logic of pairs trading while respecting the geometry of real long-only portfolios?

That question led me to design a framework that combines:

- factor residualization,
- statistical spread construction,
- mean-reversion diagnostics,
- benchmark-relative active tilts,
- convex optimization for implementability,
- and optional proxy-short constructions when direct shorting is impossible.

The result is not just a screening recipe for stock pairs. It is a **mathematical formulation of long-only relative-value implementation**.

---

## The core idea in one sentence

Instead of treating a pair trade as an unconstrained long-short position, I model it as a **benchmark-relative active tilt** that tries to reproduce the behavior of an ideal residual spread while remaining long-only, capped, and nearly factor-neutral.

That shift sounds small, but it changes the entire problem.

---

## The intuition: from clean theory to implementable portfolios

The ideal statistical trade is easy to write down. If two stocks \(i\) and \(j\) have a residual relationship that mean-reverts, then one can define a spread such as

\[
S^{\mathrm{stat}}_{i,j}(t) = \tilde r_i(t) - \beta_{ij}^{\tilde r}\, \tilde r_j(t),
\]

where \(\tilde r_i(t)\) and \(\tilde r_j(t)\) are factor-cleaned returns, and \(\beta_{ij}^{\tilde r}\) is a hedge ratio estimated from a training window.

If this spread behaves like a stationary or mean-reverting process, it becomes a candidate trading signal.

But an institutional portfolio manager often cannot literally implement “\(+1\) unit of \(i\), \(-\beta\) units of \(j\).” They start from a **benchmark** \(\mathbf b\) and are only allowed to create **active deviations** \(\mathbf a\) such that the final portfolio remains long-only:

\[
\mathbf w = \mathbf b + \mathbf a, \qquad \sum_k a_k = 0, \qquad \mathbf w \ge 0.
\]

So the real problem becomes this:

> Find an active portfolio whose return behaves as closely as possible to the intended pair spread, without violating benchmark, cap, liquidity, or factor constraints.

That is the heart of the project.

---

## Step 1 — Remove the common drivers first

A raw price or return spread can look attractive simply because both assets share exposure to the same broad drivers: market, sector, size, value, momentum, or macro shocks. I therefore start with a residualization step.

For each stock \(k\), I fit a linear factor model on a rolling training window:

\[
r_k(t)=\alpha_k + {\beta^{r,f}_k}^\top \mathbf f_t+\varepsilon_k(t),
\]

and define the residual return

\[
\tilde r_k(t)=r_k(t)-\widehat\alpha_k-(\widehat\beta_k^{r,f})^\top \mathbf f_t.
\]

This is conceptually important. The goal is not to trade raw co-movement. The goal is to trade **idiosyncratic relative dislocation**, after accounting for common factors.

That immediately makes the project more robust and more interpretable:

- the spread is less contaminated by market-wide moves,
- the signal is closer to stock-specific relative value,
- and implementation constraints can later be expressed in terms of factor neutrality.

---

## Step 2 — Build a statistical spread, not just a correlation screen

Within a universe of candidate names, ideally inside a common sector or sub-industry, I construct candidate pairs and estimate a hedge ratio on residualized returns:

\[
\beta_{ij}^{\tilde r} = \arg\min_{\beta\in\mathbb R}\sum_{t\in W_{\mathrm{train}}}
\big(\tilde r_i(t) - \beta\,\tilde r_j(t)\big)^2.
\]

This gives the statistical spread

\[
S^{\mathrm{stat}}_{i,j}(t)=\tilde r_i(t) - \beta_{ij}^{\tilde r}\, \tilde r_j(t).
\]

I deliberately moved away from the common habit of using correlation as the main selection tool. Correlation can help rank candidates, but it does not answer the real question: **does the residual relationship revert in a stable and tradable way?**

So I treat mean reversion as the real gate.

---

## Step 3 — Diagnose mean reversion with explicit temporal gates

A spread is only useful if its reversion behavior is stable enough to trade. To formalize that, I model the spread as an AR(1) process,

\[
X_t = \mu + \phi (X_{t-1} - \mu) + \eta_t,
\]

and compute the implied half-life

\[
t_{1/2} = -\frac{\log 2}{\log |\phi|}, \qquad 0<|\phi|<1.
\]

The half-life gives a useful operational interpretation: how long does it take for a deviation to shrink by half? This lets me reject two opposite pathologies:

- **too fast**, where the process may be mostly noise or microstructure artifacts,
- **too slow**, where capital could be tied up in a weak or untradable signal.

I then add stability checks across train splits, and structural break tests such as CUSUM or Chow-type diagnostics. This matters a lot. A pair is not interesting because it once looked mean-reverting. It is interesting if that behavior survives simple perturbations of the estimation window.

In other words, I am trying to build a pair-selection system that treats **temporal robustness as a first-class object**.

---

## Step 4 — Reframe the trade as a benchmark-relative active tilt

This is the part of the project that I find most interesting.

Instead of implementing an abstract long-short spread, I define a benchmark portfolio \(\mathbf b\) and an active weight vector \(\mathbf a\), with

\[
\mathbf w = \mathbf b + \mathbf a,
\qquad
\sum_i a_i = 0.
\]

The active return is then

\[
S^{\mathrm{act}}(t) = \mathbf a^\top \mathbf r(t),
\]

and the residualized active spread becomes

\[
\tilde S^{\mathrm{act}}(t) = \mathbf a^\top \tilde{\mathbf r}(t).
\]

This makes the implementation target explicit: I want \(\tilde S^{\mathrm{act}}\) to behave as closely as possible to the ideal statistical spread \(S^{\mathrm{stat}}_{i,j}\).

To measure the gap, I define the implementation error

\[
E_{i,j}(t) := \tilde S^{\mathrm{act}}(t) - S^{\mathrm{stat}}_{i,j}(t).
\]

This quantity is extremely useful because it turns a vague practical concern into a mathematical object. It lets me ask principled questions such as:

- How much does long-only implementation distort the statistical trade?
- How much of that distortion comes from imperfect funding or factor leakage?
- When does the implementable version remain faithful enough to the ideal spread to keep trading it?

That is one of the conceptual contributions of the project: **treating implementability itself as a measurable error process**.

---

## Step 5 — Introduce purity as a practical long-only concept

A classical pair trade says: fund the overweight in asset \(i\) by underweighting asset \(j\). In long-only reality, that is not always fully possible. The benchmark may hold little of \(j\), the caps may be tight, or factor constraints may force part of the funding to come from other names.

To capture that, I introduce a notion of **pair purity**.

Suppose I want to overweight asset \(i\) by at least \(\Delta\) and underweight asset \(j\) by at least \(\Delta\). Then an active tilt implements the pair *purely* at level \(p_\star\) if at least a fraction \(p_\star\) of the requested overweight is directly funded by reducing \(j\):

\[
a_i \ge \Delta,
\qquad
\frac{-a_j}{\Delta} \ge p_\star,
\qquad
w_j=b_j+a_j>0.
\]

This is a simple idea, but it gives a very clean way to reason about the tension between statistical intent and portfolio constraints.

- If purity is high, the implemented trade is close to the intended pair.
- If purity is low, the “pair trade” is in fact being funded by a diffuse basket of other names.

So purity becomes a bridge between **signal semantics** and **portfolio engineering**.

---

## Step 6 — Solve implementability with convex optimization

Once the statistical pair has passed the mean-reversion gates, I solve a constrained quadratic program to obtain the actual active weights.

The optimization objective balances two goals:

1. minimize unintended factor exposures,
2. keep the active tilt small and stable.

The formulation is

\[
\begin{aligned}
\min_{\mathbf a\in\mathbb R^{|U|}} \quad &
\|\widehat{\mathbf B}\,\mathbf a\|_2^2 + \rho \|\mathbf a\|_2^2 \\
\text{s.t.}\quad
& \mathbf 1^\top \mathbf a = 0, \\
& \mathbf b+\mathbf a \ge 0,\quad (\mathbf b+\mathbf a)_k \le c_{\max}, \\
& a_i \ge \Delta,\quad a_j \le -\Delta, \\
& \|\mathbf a\|_1 \le A_{\max}.
\end{aligned}
\]

This formulation is attractive for several reasons.

First, it is convex, so it is tractable and stable. Second, it encodes in one place the main institutional realities of portfolio construction:

- budget neutrality,
- long-only feasibility,
- name-level caps,
- benchmark-relative active risk limits,
- and approximate factor neutrality.

The optimization does not discover the pair. The statistics discover the pair. The QP answers a different question:

> Can the portfolio actually express that pair in a realistic long-only setting?

That separation between *signal validity* and *implementation feasibility* is central to the whole framework.

---

## Step 7 — When you cannot short, approximate the short geometrically

One of the optional ideas in the project is a **proxy-short** mechanism.

If a direct underweight in asset \(j\) is not feasible enough, one can try to approximate the negative return of \(j\) using a nonnegative combination of other tradable instruments, such as sector ETFs, peers, or basis assets. Formally, I solve a nonnegative sparse regression problem of the form

\[
\min_{\mathbf h_j\ge 0}
\|\mathbf Z\mathbf h_j-\mathbf y_j\|_2^2 + \lambda\|\mathbf h_j\|_1,
\]

where \(\mathbf y_j\) is the time series of \(-r_j\) and \(\mathbf Z\) stacks the returns of the basis instruments.

Geometrically, this asks whether the negative-return series of \(j\) lies close to the **long-only cone** spanned by a basis set.

I like this part because it reframes a financial constraint in a very clean geometric way. The question is no longer “can I short this stock?” but rather:

> Is the return profile of shorting this stock approximately representable inside a cone generated by long-only instruments?

That opens the door to a richer design space, though it also introduces tracking error, turnover, and stability concerns that must be explicitly gated.

---

## The overall pipeline

At a high level, the workflow becomes:

1. **Pre-filter the universe** for liquidity and sector coherence.
2. **Fit factor models** on rolling windows and compute residual returns.
3. **Generate candidate pairs** within sub-industries.
4. **Estimate residual spreads** and hedge ratios.
5. **Apply mean-reversion and stability gates** using AR(1), half-life, split stability, and break tests.
6. **Check simple implementability bounds** before optimization.
7. **Solve a factor-aware long-only QP** to obtain feasible active tilts.
8. **Reject candidates** that fail purity, cap, or factor constraints.
9. **Monitor the implemented spread out of sample** and re-estimate regularly.

What emerges is not a single predictive model, but a **decision system** that combines statistical diagnostics, portfolio geometry, and constrained optimization.

---

## What I find intellectually interesting about this work

This project sits at an intersection I enjoy: the point where elegant statistical ideas collide with real-world structure.

There are three aspects I find particularly compelling.

### 1. It turns a finance heuristic into a formal constrained problem

Pairs trading is often described heuristically. Here, the objective is to push it toward a more explicit mathematical architecture:

- define the signal,
- define the feasibility set,
- define the implementation gap,
- define the acceptance gates.

That makes the reasoning more modular and auditable.

### 2. It separates *discovery* from *execution*

A pair may be statistically attractive and still operationally poor. Another may be slightly weaker statistically but much cleaner to implement. The framework makes that distinction visible instead of collapsing everything into one score.

### 3. It gives a language for long-only relative value

Long-short relative value is well studied. Long-only relative value is conceptually trickier because it must borrow its expressivity from benchmark tilts, funding structure, and factor control. I wanted to build a language for that problem that is both mathematically clean and portfolio-aware.

---

## Limits and caveats

This is a research project, not a claim of production alpha.

There are many limitations that matter:

- residualization depends on the chosen factor set,
- AR(1) or OU diagnostics are simplifications of real spread dynamics,
- break tests have limited power in short samples,
- optimization solutions can be sensitive when factors are collinear,
- proxy-short constructions may look good in-sample but be unstable out of sample,
- and transaction costs, turnover, and market impact can easily dominate a naive backtest.

I view the current framework as a **research scaffold**: a principled way to study and prototype long-only pair implementation, not a finished live trading engine.

---

## Where I would take it next

There are several directions I would like to push further.

### Richer spread dynamics

AR(1) is interpretable, but richer state-space or regime-switching models could better capture spread behavior through market regimes.

### Stronger factor handling

The current formulation uses linear factor residualization and factor-neutrality penalties. A natural extension would be dynamic betas, hierarchical factor structures, or robust covariance-aware penalties.

### Transaction-cost-aware optimization

The next practical step would be to include turnover penalties and explicit capacity constraints directly in the optimization layer.

### Better out-of-sample validation

A proper research continuation would require a full walk-forward study with realistic rebalancing cadence, slippage assumptions, and stress tests around structural breaks.

---

## Final reflection

What I like most about this project is that it starts from a deceptively simple idea and reveals how much structure sits underneath once you ask for implementability.

A pair trade is not just a statistical relationship between two stocks. In a constrained portfolio, it is also a question of geometry, funding, factor neutrality, capacity, and stability.

That is the thread I tried to follow here: keep the original intuition of statistical arbitrage, but rebuild it in a way that respects the actual mechanics of benchmarked long-only portfolios.

If I were to summarize the project in one line, I would say this:

> It is an attempt to reformulate pairs trading as a constrained representation problem: how to represent an ideal relative-value signal inside the feasible cone of a real long-only portfolio.

That, to me, is what makes it worth studying.

---

## Technical appendix snapshot

A few of the key mathematical objects used in the project are:

- **factor model**
  \[
  r_k(t)=\alpha_k + {\beta^{r,f}_k}^\top \mathbf f_t+\varepsilon_k(t),
  \]

- **residualized return**
  \[
  \tilde r_k(t)=r_k(t)-\widehat\alpha_k-(\widehat\beta_k^{r,f})^\top \mathbf f_t,
  \]

- **statistical spread**
  \[
  S^{\mathrm{stat}}_{i,j}(t)=\tilde r_i(t) - \beta_{ij}^{\tilde r}\, \tilde r_j(t),
  \]

- **implemented active spread**
  \[
  \tilde S^{\mathrm{act}}(t)=\mathbf a^\top \tilde{\mathbf r}(t),
  \]

- **implementation error**
  \[
  E_{i,j}(t)=\tilde S^{\mathrm{act}}(t)-S^{\mathrm{stat}}_{i,j}(t),
  \]

- **AR(1) mean-reversion model**
  \[
  X_t = \mu + \phi (X_{t-1} - \mu) + \eta_t,
  \]

- **half-life**
  \[
  t_{1/2} = -\frac{\log 2}{\log |\phi|}.
  \]

These ingredients are all discussed in much more detail in my project notes, where I also sketch theoretical claims around implementation error control, out-of-sample stability, and selection gates.
