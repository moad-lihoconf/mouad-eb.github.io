---
layout: page
title: LLM-Based Contract Compliance System
permalink: /projects/llm-contract-compliance/
---

# LLM-Based Contract Intelligence (Saint-Gobain)

**Senior Data Scientist Project (2024–Present)** | Deployed to production, adopted by 3 legal teams.

---

## The Problem: High-Stakes Document Review

Reviewing complex industrial contracts for legal compliance is traditionally a slow, manual process. Commercial LLM pipelines often suffer from **instability** (varying results for the same prompt) and **hallucinations** when grounding is not robust.

## Technical Architecture

I architected and led the development of a retrieval-grounded (RAG) contract QA + clause-editing workflow.

### 1. Robust Vector Search + Grounding
- **Engine**: PostgreSQL with **pgvector**.
- **Indexing**: Clause-level hierarchical embeddings using OpenAI and Hugging Face models.
- **Stability Fix**: Implemented a **hybrid similarity tie-breaker** (Semantic + TF-IDF) that reduced result variance from ~10% to <1% across reruns. 

### 2. Human-in-the-Loop Workflow
- **Element-Level Patching**: Developed a mechanism to propose individual clause edits that can be automatically re-evaluated for compliance.
- **Audit Trails**: Fully transparent reasoning traces showing exactly which document fragments supported a "Compliant" or "Non-Compliant" verdict.

### 3. Production Readiness
- **FastAPI Backend**: Built for high-throughput asynchronous processing.
- **Monitoring**: Datadog integration for latency tracking and cost monitoring.
- **Pydantic Validation**: Strict schema enforcement to ensure LLM outputs follow legal checklist structures.

## Impact & Adoption

- **User Base**: Actively used by over **50+ legal experts** within Saint-Gobain.
- **Efficiency**: Significantly reduced the time required for initial compliance screening and clause localization.
- **Reliability**: Achieved near-deterministic stability in evidence retrieval through caching and ranked tie-breaking.

---

## Technical Stack
- **Backend**: Python, FastAPI, Pydantic, PostgreSQL/pgvector
- **AI/LLM**: OpenAI API, LangChain (customized), Sentence-Transformers
- **Infra**: Azure, Datadog, Docker
