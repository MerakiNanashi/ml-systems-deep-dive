# Architecture — Airbnb Search Ranking (Learning to Rank Diversely)

## 1. Problem Framing

The system optimizes **expected bookings over a ranked list**, not individual listings.

\[
E[\text{bookings}] = \sum_{j} P_{\text{booking}}(l_j) \cdot P_{\text{attention}}(j)
\]

Sorting by booking probability maximizes this objective and also aligns with **NDCG optimization**.   

However, booking value (≈ price) introduces a second implicit objective:

\[
\text{Value}(l) = P_{\text{booking}}(l) \cdot v_l
\]

Thus, the system implicitly faces a **dual objective**:
- maximize booking volume  
- capture high-value bookings  

This is a **set-level optimization problem**, since utility depends on how items are arranged across positions.

---

## 2. Core Components

### (A) Base Ranking Model

The system uses **pairwise learning-to-rank**:

- Input: \((l_x, l_y)\)  
- Output: \(P(l_x > l_y)\)

Training:

\[
\mathcal{L} = -\log \left( \frac{e^{f(l_x)}}{e^{f(l_x)} + e^{f(l_y)}} \right)
\]

This learns:

- lower price → higher score  
- better reviews → higher score  

Key equivalence:

\[
P(l_x > l_y) > 0.5 \;\Longleftrightarrow\; P_{\text{booking}}(l_x) > P_{\text{booking}}(l_y)
\]

Thus, **pairwise ranking ≡ sorting by booking probability**. 

---

### (B) Majority Principle (Critical)

Ranking is constructed greedily:

\[
\pi = \text{sort}(l_i \text{ by } P_{\text{booking}}(l_i))
\]

Interpretation:

- each decision favors the item preferred by **>50% of past users**
- applied at every position  

Result:

- **homogeneous rankings**
- dominance of:
  - low-price listings  
  - high-frequency preferences  

This behavior is *structural*, not incidental.

---

### (C) Problem: Preference Skew

User preference follows a **Pareto distribution**:

- ~80% bookings → low-value listings  
- ~20% bookings → ~50% value  

This creates a conflict:

\[
\max \sum P(l) \;\neq\; \max \sum P(l)\cdot v_l
\]

Implication:

- optimizing for volume suppresses high-value listings  
- ranking reflects majority, not total utility  

---

### (D) Similarity Model (Key Innovation)

To address redundancy, a second model is introduced:

- Input: \((l_i, l_j)\)  
- Output: similarity score \(s(l_i, l_j)\)

Similarity is **learned from user behavior**, not predefined.

Interpretation:

- high similarity ⇒ redundant choice  
- low similarity ⇒ adds new utility to list  

---

### (E) Reranking Algorithm

Ranking becomes **sequential and conditional**.

At each step:

1. select highest scoring listing  
2. adjust remaining scores  

\[
\text{score}(l) = P_{\text{booking}}(l) - \sum_{k} \lambda^k \cdot s(l, l_k)
\]

Simplified interpretation:

\[
\textbf{Utility(list)} = \underbrace{\sum U(l_i)}_{\text{relevance}} - \underbrace{\sum \text{similarity}}_{\text{redundancy}}
\]

or

\[
\textbf{Utility(list)} = \text{raw value} - \text{overlap penalty}
\]

This produces:

- reduced redundancy  
- increased coverage of preference space  

---

## 3. Training Pipeline

### Base Model

- Data:
  - search logs  
  - booked vs not-booked pairs  
- Objective:
  - learn \(P_{\text{booking}}(l)\)

---

### Similarity Model

Constructed using **conditional behavior**:

- select top-ranked listing as **antecedent**  
- discard sessions where top result was booked  
- create pairs from lower-ranked items  

Training signal:

\[
\text{Booked}(l_x) \Rightarrow l_x \text{ should be dissimilar to antecedent}
\]

Thus, similarity captures:

- **what users skip vs what they choose next**

---

## 4. System Insight

The system approximates a **multi-objective ranking problem**:

\[
\text{maximize utility(list)} \approx \sum P(l_i) - \text{redundancy penalty}
\]

Instead of directly optimizing:

\[
\sum P(l_i)\cdot v_i
\]

it introduces diversity as a **constraint on redundancy**, not a new objective.

Key interpretation:

- base model → captures **majority relevance**  
- similarity model → corrects **redundant exposure**  

This allows:

- improved list-level utility  
- without redefining evaluation metric (NDCG)

---

## 5. Explicit vs Implied Components

### Explicit (from article + paper)

- Pairwise LTR with cross-entropy loss  
- Ranking by booking probability  
- Majority preference dominance  
- Pareto skew in booking value  
- Conditional ranking via antecedent listings  
- Decomposition:
  \[
  f(l) = f_\theta(l) - s_\phi(l, l')
  \] 
:contentReference[oaicite:2]{index=2}  

---

### Implied (not directly stated)

- Two-stage system (retrieval → ranking)  
- Fixed candidate set (due to \(O(N^2)\) reranking)  
- Feature store for large-scale inputs  
- Exposure bias in logged data  
- Limited personalization → diversity as proxy  
- Greedy approximation of set optimization  

These are necessary for the system to function but are not explicitly described.
---

## 6. Key Takeaway

The system does not change *what* is optimized (bookings), but changes *how* utility is computed:

\[
\text{Item utility} \rightarrow \text{Marginal utility given previous selections}
\]

Diversity emerges as a **correction to independence assumptions in ranking**, turning:

\[
\text{independent scoring} \;\rightarrow\; \text{conditional scoring}
\]