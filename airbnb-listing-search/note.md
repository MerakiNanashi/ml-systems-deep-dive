# ML System Extraction Template

Link: [Learning to rank diversely](https://medium.com/airbnb-engineering/learning-to-rank-diversely-add6b1929621)

Context: 

---

## 1. Problem & Objective
- What is the system optimizing for? (e.g., bookings, CTR, retention)
    - Millions of users (hosts and guests) -> NN selects indiviual listings for guests, as well as results for overall collection of listings (enhanced)
    - optimizing overall collection of listings



- What is the prediction target?
    - comparing 2 listings -> which has higher prob of getting booked

- What constraints are mentioned? (latency, scale, freshness)
    - Generalizing the pattern over various cities, seasons and users
    
- Why is this problem difficult?
    - Pareto principle (80/20 -> 20% driving revenue)
    - Can't let the model focus solely on majority preference as the high value booking comes from high value users (20% -> minority)
    - Paerto and Majority principle conflict


H0:
Paerto Principle conflict with Majority Principle

