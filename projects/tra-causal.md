---
layout: page
title: Topological Residual Asymmetry
permalink: /projects/tra-causal/
---

# Topological Residual Asymmetry for Bivariate Causal Direction

**Under Review at ICML 2026** | [arXiv:2602.00427](https://arxiv.org/abs/2602.00427)

---

## Overview

**Topological Residual Asymmetry (TRA)** is a novel method for detecting causal direction from regressor–residual point clouds. It addresses the fundamental problem of identifying whether $X \rightarrow Y$ or $Y \rightarrow X$ by analyzing the topological structure of the residuals.

## Technical Approach

### 1. Geometric Graph Construction
We model the joint distribution of regressors and residuals using **random geometric graphs**. This allows us to capture the underlying manifold structure of the data which classical statistical tests might miss.

### 2. $0$-Dimensional Persistent Homology
The core of TRA lies in using **0-dim persistent homology** to quantify the "connectedness" and "spread" of the residual point cloud across the regressor's domain.
- **Asymmetry Detection**: We measure the persistent homology of residuals in both directions ($X \rightarrow Y$ vs $Y \rightarrow X$).
- **Principled Abstention**: TRA includes a built-in mechanism for calibrated abstention under confounding or low-signal regimes, ensuring high-confidence predictions.

### 3. Theoretical Foundations
The project includes rigorous proofs for:
- **Consistency**: The TRA statistic converges to the true causal direction as sample size increases.
- **Finite-Sample Calibration**: Methods for valid p-value estimation and thresholding in small datasets.

## Key Outcomes

- **Robustness**: Outperforms standard additive noise models (ANM) on datasets with complex noise distributions and non-linearities.
- **Performance**: Extensive validation on synthetic and real-world (Cause-Effect Pairs) benchmarks.

---

## Technical Stack
- **Languages**: Python
- **Libraries**: Giotto-tda, Gudhi, scikit-learn, NumPy, SciPy
- **Domain**: Causal Discovery, Topological Data Analysis (TDA), Random Graphs
