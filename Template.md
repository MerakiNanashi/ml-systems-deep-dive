# ML System Extraction Template

Use this template to extract key information from any ML system article, paper, or engineering blog. Focus on **signal, not verbosity**.

---

## 1. Problem & Objective
- What is the system optimizing for? (e.g., bookings, CTR, retention)
- What is the prediction target?
- What constraints are mentioned? (latency, scale, freshness)
- Why is this problem difficult?

---

## 2. Inputs & Data
- Core entities (e.g., user, item, query, context)
- Data sources used
- Labels available (clicks, conversions, ratings, etc.)
- Data challenges:
  - sparsity
  - imbalance
  - noise

---

## 3. System Boundaries
- What part of the product does this system control?
- What happens before this system?
- What happens after this system?
- What is out-of-scope?

---

## 4. High-Level Architecture
- End-to-end pipeline stages
- Online vs offline components
- Latency-critical components
- Rewrite architecture in your own words

---

## 5. Candidate Generation / Retrieval
- How is the search space reduced?
- Techniques used:
  - heuristics
  - embeddings
  - filtering
- Size of candidate set (if available)
- Trade-off:
  - recall vs latency

---

## 6. Feature Engineering
- User features
- Item/listing features
- Contextual features
- Feature computation:
  - offline (precomputed)
  - online (real-time)
- Temporal or behavioral aggregation

---

## 7. Model(s)
- Model type(s)
- Inputs → outputs
- Training objective
- Why this model (if stated or inferred)

---

## 8. Training & Updating (Fine-Tuning Perspective)
- How is the model trained?
- Training data source
- Update frequency:
  - batch retraining
  - online learning
- Embedding training (if applicable)
- Task-specific adaptation (fine-tuning behavior)

---

## 9. Evaluation
- Offline metrics:
  - Recall@K
  - NDCG
  - Precision
- Online metrics:
  - CTR
  - conversion rate
- Observed or implied mismatch between offline and online metrics

---

## 10. Serving & Inference
- Real-time vs batch inference
- Latency constraints
- Optimization techniques:
  - caching
  - batching
  - async processing

---

## 11. Trade-offs
- Accuracy vs latency  
- Personalization vs generalization  
- Freshness vs stability  
- Cost vs performance  

---

## 12. Failure Modes / Challenges
- Cold start
- Data quality issues
- Feedback loops
- Bias (popularity, exposure)
- System bottlenecks

---

## 13. Missing Pieces
- What is not explained?
- What assumptions are being made?
- Where could the system break at scale?

---

## 14. Transferable Patterns
- Reusable system design patterns
- Ideas applicable to other ML systems

---

## Usage Notes
- Fill this in **bullet points only**
- Do not copy text from sources
- Keep it concise and structured
- Use this as raw material for your final deep dive