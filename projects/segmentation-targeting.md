---
layout: page
title: Customer Segmentation & Targeting
permalink: /projects/segmentation-targeting/
---

# High-Precision Customer Segmentation & Targeting (Saint-Gobain)

**Production Data Science Project (2021)** | Shipped to production, achieved 40% CTR increase.

---

## Objective: Behavioral Discovery at Scale

The goal was to replace a legacy heuristic-based targeting system with an automated, data-driven segmentation pipeline that captures complex customer purchase patterns and scales to millions of transactions.

## Technical Architecture

### 1. Feature Engineering & Selection
- **Purchase History Signals**: Engineered compact descriptors from purchase sequences, including recency, frequency, and monetary (RFM) trends.
- **Unsupervised Selection**: Used **Laplacian Score** to identify the most informative features for clustering, reducing noise and dimensionality.
- **UMAP Embeddings**: Learned low-dimensional representations for high-cardinality categorical variables to mitigate the "curse of dimensionality" in distance-based clustering.

### 2. Multi-Stage Scalable Clustering
Built a robust two-stage clustering procedure to handle large-scale discovery:
- **Stage 1 (BIRCH)**: Large-scale micro-cluster discovery with a custom model-selection objective balancing Silhouette scores and business size constraints.
- **Stage 2 (Agglomerative)**: Consolidation of micro-clusters into stable, actionable segments.
- **Adaptive Scaling**: Applied feature-specific scaling to stabilize heterogeneous ranges across purchase density and temporal signals.

### 3. Tactical Targeting & Validation
- **Purchase-Density Estimation**: Used **Kernel Density Estimation (KDE)** to rank clusters for specific campaigns.
- **Stability Diagnostics**: Quantified temporal stability by comparing predicted cluster ordering to next-period realized purchase densities using Wasserstein distance.

## Results & Impact

- **Performance**: Increased click-through rate (CTR) by **40%** compared to the previous production baseline.
- **Efficiency**: Reduced end-to-end clustering runtime by **10$\times$** (85% reduction) through optimized feature selection and BIRCH indexing.
- **Deployment**: Successfully transferred personalized recommendations to marketing engines for over 100+ active campaigns.

---

## Technical Stack
- **Languages**: Python (scikit-learn, pandas, NumPy)
- **Visualization**: UMAP, Matplotlib/Seaborn
- **Data/Infrastructure**: SQL, Joblib for parallelization
