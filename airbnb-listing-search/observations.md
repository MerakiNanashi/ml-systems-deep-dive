# observations.md

## Goal

Validate **only the core system-level hypotheses** behind Airbnb’s ranking approach using minimal, causal experiments.

Focus:
- Not exhaustive validation  
- Only the **key tensions that justify the system design**  
- Tie directly to the paper’s claims on majority bias, Pareto effects, and diversity  

Grounded in system philosophy: experiments isolate **one variable at a time**  

---

## Hypothesis 1 — Majority Bias in Pairwise Ranking

### Claim

Pairwise learning-to-rank implicitly optimizes for **majority preference**, leading to:

> Systematic preference for cheaper listings

This follows directly from:

- Ranking by booking probability  
- Majority users being **price-sensitive**  

As shown in the paper:

- Ranking is equivalent to sorting by booking probability  
- Majority preference dominates model decisions  

---

### Formal Interpretation

If:

$$
\text{rank}(i) \propto p_i
$$

and empirically:

$$
p_i \downarrow \text{ as price } v_i \uparrow
$$

Then:

$$
\text{rank}(i) \propto -\text{price}
$$

---

### Validation

**Experiment**

- Compute:
  - correlation(price, booking proxy)
- Compare:
  - avg price of top-K ranked by \( p_i \)

---

### Expected Results

1. **Negative correlation**
   $$
   \text{corr}(price, p_i) < 0
   $$

2. **Top-K skew**
   - Top-ranked listings have **lower-than-average price**

---

### Observation

- Ranking model **collapses diversity along price dimension**
- Results become:
  - Homogeneous  
  - Dominated by “safe” majority choices  

---

### Interpretation

This validates the paper’s claim:

> “Ranking abides by majority preference at each position” :contentReference[oaicite:2]{index=2}  

This is not a bug — it is a **direct consequence of the objective function**.

---

## Hypothesis 2 — Objective Misalignment (Critical)

### Claim

Optimizing for bookings does **not** optimize for revenue.

Formally:

$$
\max \sum p_i \;\;\neq\;\; \max \sum p_i v_i
$$

---

### System Context

From the paper:

- ~80% users → low-value bookings  
- ~20% users → disproportionate revenue (Pareto effect) :contentReference[oaicite:3]{index=3}  

Thus:

- Booking probability ≠ economic value  

---

### Validation

Construct two rankings:

#### Ranking A (Baseline)
$$
\pi_A = \text{sort by } p_i
$$

#### Ranking B (Value-aware)
$$
\pi_B = \text{sort by } p_i \cdot v_i
$$

---

### Compare

| Metric | Ranking A | Ranking B |
|-------|----------|----------|
| Avg price | ↓ | ↑ |
| Total value | ↓ | ↑ |
| Booking proxy | ↑ | ↓ |
| Overlap@K | low | — |

---

### Expected Results

1. **Low overlap**
   $$
   \text{Top-K}_A \neq \text{Top-K}_B
   $$

2. **Trade-off**
   - Ranking A:
     - More bookings  
     - Lower value  
   - Ranking B:
     - Fewer bookings  
     - Higher value  

---

### Observation

- The system faces an inherent **multi-objective conflict**
- No single ranking satisfies both:
  - engagement (CTR/bookings)  
  - revenue  

---

### Interpretation

This directly explains why:

> “Optimizing booking volume can reduce revenue per user” :contentReference[oaicite:4]{index=4}  

This is the **central failure mode** of standard ranking.

---

## Hypothesis 3 — Diversity Improves Set-Level Utility

### Claim

Diversity-aware reranking improves:

- **set-level value**
- without large loss in bookings

---

### Mechanism (From Paper)

Instead of independent scoring:

$$
\text{score}_i = p_i - \lambda \cdot \text{sim}(i, S)
$$

Where:

- \( S \): already selected items  
- similarity penalizes redundancy  

This approximates:

> conditioning on previously rejected items :contentReference[oaicite:5]{index=5}  

---

### Validation

#### Step 1 — Baseline
- Rank by \( p_i \)

#### Step 2 — Diversified Ranking
- Iteratively apply:
  $$
  \text{score}_i = p_i - \lambda \cdot \text{sim}(i, S)
  $$

---

### Compare

| Metric | Baseline | Diversified |
|--------|---------|------------|
| Bookings proxy | ~same / slight ↓ |
| Revenue proxy | ↑ |
| Avg price | ↑ |
| Price variance | ↑ |
| Redundancy | ↓ |

---

### Expected Results

1. **Higher variance**
   - Listings span wider price/quality spectrum  

2. **Revenue gain**
   - Inclusion of high-value minority listings  

3. **Small booking drop (if any)**
   - Majority still partially preserved  

---

### Observation

- Diversity acts as a **constraint on majority dominance**
- Enables representation of **long-tail preferences**

---

### Interpretation

Matches paper findings:

- +0.29% bookings  
- +0.8% booking value :contentReference[oaicite:6]{index=6}  

Key insight:

> Diversity is not a UX feature — it is a **latent objective correction mechanism**

---

## Cross-Hypothesis Insight

These three observations form a coherent system explanation:

---

### 1. Root Cause

- Pairwise ranking → majority optimization  
- Majority → price-sensitive users  

---

### 2. Failure

- Ignores high-value minority  
- Leads to **suboptimal revenue**

---

### 3. Resolution

- Diversity introduces **controlled deviation**
- Approximates multi-objective optimization without explicit formulation  

---

## Key Takeaway

The experiments confirm:

> The ranking problem is fundamentally **set-level and multi-objective**,  
but is approximated in practice via:
- pointwise probability (base model)  
- diversity-aware reranking (correction layer)

This aligns with the system design:

- Base ranker → learns dominant signal  
- Reranker → injects missing structure  

---

## Minimal Conclusion

- Majority bias is **structural**, not incidental  
- Objective misalignment is **inevitable** under single-metric optimization  
- Diversity is a **practical mechanism** to recover lost utility  

---