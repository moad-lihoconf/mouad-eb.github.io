---
layout: post
title: "LLM-Based Contract Compliance System"
date: 2025-01-02
categories: [industry, llm]
tags: [LLM, RAG, pgvector, FastAPI]
---

# Building an LLM-Based Contract Compliance System

This post describes the design of a production system for verifying contract compliance using LLMs and retrieval-augmented generation.

---

## Problem

Legal and procurement teams need to verify that supplier contracts meet compliance checklists—dozens of conditions across hundreds of pages. Manual review is slow and inconsistent.

The goal: automate checklist verification with evidence linking, while ensuring stable and auditable outputs.

---

## System Design

### Architecture Overview

```
Contracts (PDF) → Parser → Clause Database (PostgreSQL + pgvector)
                                    ↓
Checklist Items → Retriever → Candidate Clauses
                                    ↓
                       LLM Evaluator → Structured Verdict + Evidence
```

### Key Components

1. **Clause Extraction**: Parse contracts into clause-level chunks with metadata (section, page, context)

2. **Vector Store**: Store clause embeddings in PostgreSQL with pgvector. Each clause is embedded using a sentence transformer.

3. **Retriever**: For each checklist condition, retrieve top-k candidate clauses by embedding similarity

4. **LLM Evaluator**: Use an LLM with structured outputs (function calling) to:
   - Decide if the condition is met
   - Cite specific clause(s) as evidence
   - Provide reasoning

---

## Stability Challenge

Early versions showed ~10% variance in checklist-to-clause mappings across repeated runs. Same contract, same checklist, different answers.

### Root Causes

1. **Embedding tie-breaking**: When multiple clauses have near-identical similarity scores, the ordering is unstable
2. **LLM sampling**: Temperature > 0 introduces variation
3. **No caching**: Repeated processing recomputed everything

### Solutions

1. **Hybrid ranking**: Break ties using TF-IDF overlap between checklist text and clause text

2. **Deterministic inference**: Set temperature to 0, use consistent prompts

3. **Caching layer**: Cache retrieval results and LLM outputs keyed by (contract_hash, checklist_hash)

Result: Variance dropped from ~10% to <1%.

---

## Monitoring

We track:

- **Decision stability**: Run the same input twice, measure agreement rate
- **Evidence coverage**: % of verdicts with linked clauses
- **Latency**: p50/p95 response times

---

## Stack

| Component | Technology |
|-----------|------------|
| Database | PostgreSQL + pgvector |
| API | FastAPI |
| LLM | Azure OpenAI |
| Embeddings | Sentence Transformers |
| Orchestration | Python async |

---

## Takeaways

- Retrieval-augmented systems need stability by design, not as an afterthought
- Hybrid ranking (semantic + lexical) helps in ambiguous cases
- Structured outputs from LLMs enable reliable downstream processing
