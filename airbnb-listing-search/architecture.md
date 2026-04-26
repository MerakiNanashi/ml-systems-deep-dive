# Airbnb Search Ranking – Learning to Rank Diversely

**Link:** https://medium.com/airbnb-engineering/learning-to-rank-diversely-add6b1929621

**Context:** This work analyzes how Airbnb improves search ranking by balancing relevance with diversity in listing results. It addresses the mismatch between majority user preferences and high-value minority behavior using a learned reranking approach.

---

## 1. Overview

#### 1.1 System Objectives

1. Maximize booking rate and total booking value (revenue optimization)  
2. Improve result quality and diversity (relevance + coverage)  
3. Optimize the entire ranked result set rather than individual listings  

#### 1.2 System Constraints

1. Generalization across domains:
    - geographic regions (cities)
    - temporal variation (seasonality)
    - heterogeneous user segments  
2. Real-time inference constraints (low-latency ranking)  
3. Trade-off between:
    - majority user preference (volume-driven)
    - minority high-value users (revenue-driven)  

#### 1.3 Challenges
1. Majority bias (data-driven learning):
    - Models trained on historical data overfit to dominant preferences (e.g., cheaper listings)  

2. Long-tail value distribution:
    - A small fraction of users contributes disproportionately to revenue (Pareto skew)  

3. Objective misalignment:
    - Optimizing for booking volume can reduce revenue per user  
    - Optimizing for high-value users can reduce overall engagement  

---

## 2. Inputs & Data

- Core entities:
  - user (guest)
  - listing
  - search query (location, dates, guests)
  - context (trip details)

- Data sources:
  - historical search logs
  - booking data
  - interaction data (clicked vs booked vs skipped)

- Labels:
  - booked vs not booked (primary)
  - implicit negatives from skipped listings

- Data challenges:
  - imbalance:
    - majority of bookings are low-cost
  - preference heterogeneity:
    - different users value price vs quality differently
  - bias:
    - logged data reflects past ranking decisions

---