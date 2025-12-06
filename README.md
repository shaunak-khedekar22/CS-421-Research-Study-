# RAG²-Style Medical QA for USMLE/MedQA

RAG²-style medical QA pipeline for USMLE/MedQA using rationale-guided retrieval, balanced multi-source FAISS search, Flan-T5 reranking, and Qwen2.5-7B-Instruct for answer generation. Includes MedQA evaluation, JSONL logs, and basic visualizations for error and performance analysis.

---

## Overview

This repository implements a simplified version of the RAG² (Rationale-Guided Retrieval-Augmented Generation) framework for multiple-choice medical question answering. The goal is to study how rationale-guided retrieval and filtering affect performance on USMLE-style questions from MedQA.

The pipeline:

1. Generates a brief **rationale** for each question.
2. Uses that rationale to perform **balanced retrieval** from multiple medical corpora with FAISS.
3. **Reranks/filters** retrieved passages with Flan-T5.
4. Uses **Qwen2.5-7B-Instruct** to produce an explanation and a single answer choice (A–E).

We log detailed outputs to JSONL and provide simple visualizations to analyze errors and performance.

---

## Pipeline Components

### 1. Rationale-Guided Retrieval

- The generator first produces a short natural-language rationale from the question.
- This rationale is used as an enriched query for the retriever.
- Retrieval is **balanced across sources** (e.g., PubMed, PMC, textbooks, guidelines), using FAISS indices to return a fixed number of passages per source.

### 2. Balanced Multi-Source FAISS Search

- Each corpus is indexed separately with FAISS.
- For each question:
  - We retrieve from all corpora.
  - We enforce a balanced top‑k across 4 sources.
- This reduces over-reliance on a single corpus and encourages diverse evidence.

### 3. Flan-T5 Reranking / Filtering

- Retrieved passages are fed to a Flan‑T5 model that scores their relevance to the question.
- Only the top N passages are kept as input context for the generator.
- This step approximates the “rationale-aware filtering” used in the RAG² framework.

### 4. Answer Generation with Qwen2.5‑7B‑Instruct

- Generator: `Qwen/Qwen2.5-7B-Instruct`
- Input: question, options, and filtered context.
- Output format is constrained:

