# Architecture — Airbnb Search Ranking (Learning to Rank Diversely)

## 1. Problem Framing

The system is designed to rank listings for a given search query such that **expected bookings are maximized**, while also capturing **booking value (revenue) implicitly**.

Let:
- \( l \) = listing  
- \( q \) = query  
- \( u \) = user  
- \( P_{\text{booking}}(l) \) = probability that listing \(l\) gets booked  
- \( P_{\text{attention}}(j) \) = probability that position \(j\) is viewed  

Then expected bookings over a ranked list are:

\[
E[\text{bookings}] = \sum_{i=1}^{N} \sum_{j=1}^{K} P_{\text{booking}}(l_{i,j}) \cdot P_{\text{attention}}(j)
\]

This implies:

- Ranking is a **set-level optimization problem**, not item-level  
- Ordering affects total utility via **position-dependent attention**

Sorting listings by booking probability maximizes both:
- expected bookings  
- **NDCG (Normalized Discounted Cumulative Gain)**   

---

## 2. Core Components

### (A) Base Ranking Model — Pairwise Learning-to-Rank (LTR)

The base model is a neural network:

\[
f_\theta(q, u, l)
\]

where:
- \( \theta \) = model parameters  

Training:

- Input: pair of listings \((l_x, l_y)\)  
- Label: which listing was booked  
- Loss: **cross-entropy**

Pairwise booking probability:

\[
P_{\text{booking}}(l_x > l_y) = \frac{e^{f_\theta(q,u,l_x)}}{e^{f_\theta(q,u,l_x)} + e^{f_\theta(q,u,l_y)}}
\]

This follows the **Bradley–Terry model** (pairwise probability model).

Key property:

\[
f_\theta(q,u,l_x) > f_\theta(q,u,l_y) \;\Longleftrightarrow\; P_{\text{booking}}(l_x) > P_{\text{booking}}(l_y)
\]

Thus:
- pairwise ranking ≡ sorting by booking probability 

Learned behavior:
- higher price → lower score  
- more reviews / quality → higher score  

Implicit outcome:
- model learns **majority preference from historical data**

---

### (B) Majority Principle

From pairwise formulation:

\[
P_{\text{booking}}(l_x > l_y) > 0.5
\]

means:
- listing preferred by **>50% of past users** is ranked higher  

Ranking procedure:

1. select listing with highest \(P_{\text{booking}}\)  
2. remove it  
3. repeat  

Result:
- each position reflects **majority preference independently**

Outcome:
- **homogeneous rankings (cheap-heavy)**  
- diversity is not modeled  

---

### (C) Problem: Preference Skew (Pareto Effect)

Empirical structure:

- ~80% bookings → low-value listings  
- ~20% bookings → ~50% booking value  

Implication:

- majority users → price-sensitive  
- minority users → quality-sensitive  

Conflict:

- maximizing bookings → favors cheap listings  
- maximizing value → requires expensive / high-quality listings  

This creates a structural mismatch between:
- **majority principle**
- **Pareto distribution of value** 

---

### (D) Similarity Model (Key Innovation)

To address redundancy, the system introduces:

\[
s_\phi(q, u, l_i, l_j)
\]

where:
- \( s_\phi \) = similarity model  
- \( \phi \) = model parameters  
- input = (candidate listing, previously selected listing)  
- output = similarity score  

Interpretation:
- higher similarity → more redundant choice  

This similarity is:
- **learned from data (not predefined)**  
- derived from user behavior (skips vs bookings)  

---

### (E) Reranking Algorithm

Ranking becomes **sequential and conditional**.

Initialize:
- compute base scores \( f_\theta(q,u,l) \)

Iterative selection:

1. select:
\[
l^* = \arg\max f_\theta(q,u,l)
\]

2. update remaining scores:

\[
\text{score}(l) = f_\theta(q,u,l) - \sum_{i=0}^{k-1} \lambda^i \cdot s_\phi(q,u,l,l_i)
\]

where:
- \( l_i \) = already selected listings  
- \( \lambda \in [0,1] \) = decay factor controlling influence  

3. repeat

Key effect:
- penalizes listings similar to those already shown  
- introduces **diversity across positions**

---

## 3. Training Pipeline

### Base Model

- Data: search logs  
- Construct pairs:
  - (booked listing, not-booked listing)  
- Optimize:
  - cross-entropy loss  
- Output:
  - booking probability estimator  

---

### Similarity Model

Training setup:

- define **antecedent listing** \(l_0\):
  - top-ranked listing in search result  

- discard:
  - sessions where \(l_0\) was booked  

- remaining sessions:
  - users skipped \(l_0\)  

Construct training pairs:
- (booked \(l_x\), not-booked \(l_y\))  
- conditioned on \(l_0\)

Objective:
- booked listing should be:
  - **preferred over others**
  - **dissimilar to antecedent**

Thus similarity is learned as:
- inverse signal of user continuation behavior  

---

## 4. System Insight (No Validation)

The system approximates a **multi-objective optimization problem**:

- maximize bookings  
- increase coverage of different preference segments  

Instead of directly optimizing:

\[
\sum P_{\text{booking}}(l) \cdot \text{value}(l)
\]

it uses:

- a **constraint-based formulation**
- diversity enforced via **similarity penalty**

Thus:

- objective remains unchanged (booking probability / NDCG)  
- optimization space is modified through **conditional scoring**

---

## 5. Implied Components (Not Explicitly Stated)

These are required for the system to function but not directly described.

### Candidate Generation Stage
- ranking operates on a **pre-filtered set of listings**  
- required due to \(O(N^2)\) reranking complexity  

---

### Feature Infrastructure
- large-scale feature inputs (listing, user, query)  
- implies:
  - offline feature computation  
  - online feature serving  

---

### Logging System
- requires:
  - impressions  
  - positions  
  - bookings vs skips  

enables:
- pairwise training  
- conditional training  

---

### Exposure Bias
- training data depends on:
  - previous ranking policy  
- model learns from:
  - shown items, not full space  

---

### Limited Personalization
- minority users not explicitly identified  
- diversity acts as:
  - **proxy for missing personalization**

---

### Greedy Approximation
- ranking is sequential greedy  
- does not guarantee:
  - globally optimal permutation  

---

### Global Trade-off Parameter

- single \( \lambda \) controls diversity  

Implication:
- same trade-off applied across:
  - users  
  - queries  
  - contexts  

---

## 6. Key Insight

The system does not change *what* is optimized; it changes *how ranking is constructed*.

- classical LTR:
  - independent scoring  

- this system:
  - **conditional marginal scoring**

Diversity emerges as a **by-product of modeling inter-item interactions**, while preserving alignment with bookings and NDCG.  

---

## References

- Airbnb Engineering Blog — Learning to Rank Diversely :contentReference[oaicite:4]{index=4}  
- Airbnb Paper (arXiv:2210.07774) :contentReference[oaicite:5]{index=5}  
- ML Systems Deep Dive Repository Structure :contentReference[oaicite:6]{index=6}  
- Extraction Template (for structuring analysis) :contentReference[oaicite:7]{index=7}  