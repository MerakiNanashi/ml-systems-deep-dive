# observations.md

## Goal

Validate **core system hypotheses** using minimal, causal experiments.

Focus:
- Only the **key mechanisms that justify the architecture**
- Each hypothesis → one experiment → one clear outcome  
- Results will be filled directly in this document  

Grounded in system-first methodology :contentReference[oaicite:0]{index=0}  

---

## Hypothesis 1 — Majority Bias in Pairwise Ranking

### Claim

Pairwise ranking optimizes for **booking probability**, which reflects **majority user preference**, leading to:

> Systematic preference for cheaper listings

From the system:

- Ranking ≈ sorting by booking probability :contentReference[oaicite:1]{index=1}  
- Majority users are price-sensitive  

---

### Formalization

$$
\text{rank}(i) \propto p_i
\quad \text{and} \quad
p_i \downarrow \text{ as } v_i \uparrow
$$

---

### Experiment

1. Compute:
   - correlation(price, booking proxy)

2. Compare:
   - avg price of top-K (ranked by \( p_i \))  
   - avg price of full dataset  

---

### Results

**Correlation**

- corr(price, booking proxy) =  

**Top-K Price Comparison**

| Metric | Value |
|--------|------|
| Avg price (Top-K) | |
| Avg price (All listings) | |

---

### Observation

-  

---

### Interpretation

- Confirms / rejects majority bias  
- Relates to dominance of majority preference in ranking  

---

## Hypothesis 2 — Objective Misalignment (Critical)

### Claim

Maximizing bookings is not equivalent to maximizing revenue:

$$
\max \sum p_i \;\;\neq\;\; \max \sum p_i v_i
$$

System implication:

- Majority (low-price) vs minority (high-value) conflict :contentReference[oaicite:2]{index=2}  

---

### Experiment

Construct two rankings:

#### Ranking A (Booking-optimized)
$$
\pi_A = \text{sort by } p_i
$$

#### Ranking B (Value-optimized)
$$
\pi_B = \text{sort by } p_i \cdot v_i
$$

---

### Metrics

Compare:

- Avg price  
- Total value  
- Booking proxy  
- Overlap@K  

---

### Results

| Metric | Ranking A (by \(p_i\)) | Ranking B (by \(p_i v_i\)) |
|--------|------------------------|-----------------------------|
| Avg price | | |
| Total value | | |
| Booking proxy | | |
| Overlap@K | | |

---

### Observation

-  

---

### Interpretation

- Quantifies trade-off between:
  - engagement (bookings)  
  - value (revenue)  
- Confirms whether objectives diverge in practice  

---

## Hypothesis 3 — Diversity Improves Set-Level Utility

### Claim

Diversity-aware reranking improves **set-level outcomes**:

- Increases value  
- Maintains (or slightly reduces) bookings  

Mechanism:

$$
\text{score}_i = p_i - \lambda \cdot \text{sim}(i, S)
$$

From system design:

- Penalizes redundancy  
- Encourages coverage across preference space :contentReference[oaicite:3]{index=3}  

---

### Experiment

1. Baseline:
   - Rank by \( p_i \)

2. Diversified:
   - Iterative reranking using similarity penalty  

---

### Metrics

Compare:

- Booking proxy  
- Total value  
- Avg price  
- Price variance  
- Redundancy (similarity within top-K)  

---

### Results

| Metric | Baseline | Diversified |
|--------|----------|-------------|
| Booking proxy | | |
| Total value | | |
| Avg price | | |
| Price variance | | |
| Redundancy | | |

---

### Observation

-  

---

### Interpretation

- Evaluates whether diversity:
  - introduces useful variation  
  - captures minority preferences  
- Links directly to paper’s reported gains in value and engagement  

---

## Cross-Hypothesis Summary

### Key Findings

1. Majority bias:
   -  

2. Objective misalignment:
   -  

3. Effect of diversity:
   -  

---

### System Insight

- Base ranker → optimizes majority signal  
- Reranker → corrects for missing objectives  

---

## Final Takeaway

- Ranking is inherently **multi-objective**
- Single-metric optimization leads to:
  - bias  
  - loss of value  
- Diversity acts as a **practical correction layer**  

---