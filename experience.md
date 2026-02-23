---
layout: page
title: Experience
permalink: /experience/
---

<link rel="stylesheet" href="{{ '/assets/css/cv.css' | relative_url }}">

<div class="cv-container">
  
  <div class="cv-section">
    <h2>Industrial & Research Leadership</h2>

    <div class="cv-card">
      <div class="header">
        <span class="title">Senior Data Scientist</span>
        <span class="date">12/2024 -- Present</span>
      </div>
      <span class="institution">Saint-Gobain, Paris-La Défense</span>
      <ul>
        <li><strong>Predictive Maintenance:</strong> Built an end-to-end failure-risk pipeline using intensity-based hazard models (Poisson/Cox-style) on asset-day exposure, producing calibrated 7-day failure probabilities.</li>
        <li><strong>Root-Cause Analysis (RCA):</strong> Developed sensor-driven RCA to localize failure drivers using hierarchical random-effects (frailty) modeling across complex plant topologies.</li>
        <li><strong>Legal AI (LLM + RAG):</strong> Built retrieval-grounded contract QA + clause-editing workflows. Improved checklist-to-evidence stability from ~10% to <1% via hybrid similarity tie-breaking (semantic + TF-IDF).</li>
      </ul>
      <div class="cv-tags">
        <span class="cv-tag">PyTorch/Pyro</span>
        <span class="cv-tag">FastAPI</span>
        <span class="cv-tag">PostgreSQL/pgvector</span>
        <span class="cv-tag">Snowflake</span>
      </div>
    </div>

    <div class="cv-card">
      <div class="header">
        <span class="title">Industrial PhD Researcher (Causal Inference / ML)</span>
        <span class="date">09/2021 -- 12/2024</span>
      </div>
      <span class="institution">Saint-Gobain / Paris-Saclay University</span>
      <ul>
        <li><strong>Dynamic ITE Estimation:</strong> Developed CDVAE (Causal Dynamic VAE) for counterfactual regression, published at **NeurIPS** and **TMLR**.</li>
        <li><strong>Sequential Policy Learning:</strong> Designed a multi-week decision framework from logged counterfactual trajectories, deployed to 100+ sales engineers.</li>
        <li><strong>Scalable Uplift:</strong> Built causal uplift pipelines for 100+ campaigns, reducing processing time by 25% via custom neural panel embeddings.</li>
      </ul>
      <div class="cv-tags">
        <span class="cv-tag">Causal Inference</span>
        <span class="cv-tag">Representation Learning</span>
        <span class="cv-tag">MLflow</span>
        <span class="cv-tag">Hydra</span>
      </div>
    </div>

    <div class="cv-card">
      <div class="header">
        <span class="title">Data Scientist (Segmentation & Targeting)</span>
        <span class="date">04/2021 -- 08/2021</span>
      </div>
      <span class="institution">Saint-Gobain, Paris-La Défense</span>
      <ul>
        <li>Shipped a production segmentation pipeline that increased CTR by **40%** and reduced clustering runtime by **10x**.</li>
        <li>Implemented a two-stage clustering procedure (BIRCH + Agglomerative) with custom model selection (silhouette + business constraints).</li>
      </ul>
    </div>
  </div>

  <div class="cv-section">
    <h2>Research Internships</h2>

    <div class="cv-card">
      <div class="header">
        <span class="title">Research Intern (Representation Learning)</span>
        <span class="date">05/2020 -- 11/2020</span>
      </div>
      <span class="institution">EDF Lab, Paris-Saclay</span>
      <ul>
        <li>Built benchmarks for deep time-series classification from smart-meter curves.</li>
        <li>Achieved ConvLSTM-level accuracy with CAE representations while cutting training time by **30x**.</li>
      </ul>
    </div>

    <div class="cv-card">
      <div class="header">
        <span class="title">Research Intern (Forecasting & Analytics)</span>
        <span class="date">10/2019 -- 03/2020</span>
      </div>
      <span class="institution">L'Oréal Paris, Paris</span>
      <ul>
        <li>Modeled product-to-tester transformations using store-level time series; analyzed stockout drivers via Cramér's V tests.</li>
        <li>Introduced stochastic time-warp augmentation via cubic-spline distortions to preserve cluster geometry.</li>
      </ul>
    </div>
  </div>

  <div class="cv-section">
    <h2>Academic Teaching</h2>
    <div class="cv-card">
      <div class="header">
        <span class="title">Assistant Teaching Professor</span>
        <span class="date">2022 -- 2024</span>
      </div>
      <span class="institution">CentraleSupélec, Paris-Saclay University</span>
      <p>Led labs and tutorials for Master in Data Science & Business Analytics (ESSEC/CentraleSupélec). Covered Supervised Learning, Ensemble Methods, and dimensionality reduction. Delivered guest lectures on <i>Introduction to Causality</i>.</p>
    </div>
  </div>
</div>
