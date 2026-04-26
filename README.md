# ML Systems Deep Dive

A structured exploration of real-world machine learning systems with a focus on **how and why they work in production**. This repository combines system design analysis, statistical reasoning, and targeted experiments to understand trade-offs in retrieval, ranking, and LLM-based pipelines.

---

## Motivation

Most ML learning resources emphasize models in isolation. In practice, performance is determined by:

- Data pipelines and feature quality  
- System architecture and latency constraints  
- Evaluation methodology and metrics  
- Trade-offs between accuracy, cost, and scalability  

This repository studies ML systems as **end-to-end decision systems**, not isolated models.

---

## Goals

- Develop **system design intuition** for ML applications  
- Understand **trade-offs under production constraints**  
- Connect **statistical reasoning** with real system behavior  
- Validate ideas through **controlled experiments**  
- Build reusable patterns for **retrieval, ranking, and LLM pipelines**

---

## Core Principles

1. **Systems over models**  
   Focus on pipelines, data flow, and integration rather than standalone models.

2. **Analysis over summarization**  
   Each study reconstructs and critiques systems instead of restating existing material.

3. **Experiments over assumptions**  
   Key ideas are validated through small, focused implementations.

4. **Trade-offs are explicit**  
   Every design decision is evaluated in terms of latency, cost, and performance.

5. **Statistical grounding**  
   Systems are analyzed through metrics, distributions, and failure probabilities.

---

## Structure

Each system is organized as:

/system-name/  
    ├── README.md # Full deep dive  
    ├── architecture.md # System design and components  
    ├── experiments/ # Focused implementations  
    ├── observations.md # Insights from experiments  
    └── improvements.md # Proposed system enhancements  


---

## Deep Dive Template

Each system follows a consistent structure to ensure comparability and depth:

### 1. Problem Definition
- What problem the system solves  
- Constraints (latency, scale, data availability)  
- Why the problem is difficult  

### 2. System Architecture
- End-to-end pipeline  
- Online vs offline components  
- Data flow and dependencies  

### 3. Design Decisions
- Model choices  
- Retrieval / ranking strategy  
- Infrastructure considerations  

### 4. Statistical Perspective
- Assumptions about data distribution  
- Metrics used and their limitations  
- Sources of bias, variance, and noise  

### 5. Experiments
- Minimal implementations of key ideas  
- Controlled variations (e.g., chunking, top-k, models)  
- Quantitative evaluation  

### 6. Observations
- Empirical findings  
- Patterns across experiments  
- Unexpected behaviors  

### 7. Trade-offs
- Precision vs recall  
- Latency vs quality  
- Cost vs performance  

### 8. Failure Modes
- Data-related failures (drift, noise, leakage)  
- Model limitations  
- System bottlenecks  

### 9. Improvements
- Alternative designs  
- Optimization strategies  
- Scaling considerations  

---

## Methodology

Each deep dive follows a repeatable process:

1. **Source Analysis**  
   Extract system details from engineering blogs, papers, and documentation  

2. **System Reconstruction**  
   Rebuild the architecture and data flow independently  

3. **Hypothesis Formation**  
   Identify key variables affecting system performance  

4. **Experimental Validation**  
   Test hypotheses using simplified implementations  

5. **Insight Generation**  
   Derive conclusions based on empirical results and system constraints  

---

## Example Focus Areas

### Retrieval-Augmented Generation (RAG)
- Chunking strategies and context construction  
- Embedding quality vs retrieval performance  
- Top-K selection and noise trade-offs  

### Ranking Systems
- Candidate generation vs ranking stages  
- Optimization for Recall@K under imbalance  
- Feature engineering and temporal signals  

### LLM Pipelines
- Structured outputs and validation  
- Latency optimization (caching, batching)  
- Cost-performance trade-offs across models  

---

## Experiments Philosophy

Experiments are intentionally:
- **Minimal**: isolate one variable at a time  
- **Reproducible**: simple datasets and pipelines  
- **Focused**: test a specific system behavior  

The objective is not to build full systems, but to understand **why certain designs emerge in production**.

---

## Background

This work builds on practical experience with:

- LLM-based evaluation pipelines processing 30K+ requests/day  
- Retrieval systems using FAISS and embedding models  
- Ranking systems optimized for Recall@K under extreme class imbalance  
- Real-time ML systems using async pipelines, caching, and batching 

---

## Contributions

Contributions are welcome, but should align with the core philosophy of the repository.

### What to contribute
- Deep dives into real-world ML systems  
- Focused experiments validating system behavior  
- Improvements to existing analyses  
- Additional perspectives on trade-offs or failure modes  

### Guidelines
- Avoid superficial summaries  
- Maintain structured analysis (follow the template)  
- Include reasoning behind design decisions  
- Keep implementations minimal and purposeful  

---

## Contact

For discussion, collaboration, or feedback:

- GitHub: https://github.com/MerakiNanashi   

---