# Deep Dive: Causal Inference & Experimentation

In logistics, you cannot always randomize orders into A/B groups. Advanced causal methods are required to measure the true impact of changes.

## 1. Switchback Testing (Network Interference)
*   **The Problem:** In a delivery marketplace, giving a feature to Rider A (treatment) affects Rider B (control) because they are competing for the same pool of orders. This is "Network Interference."
*   **The Solution:** Toggling the feature for the entire city in 30-minute intervals (Switchbacks). This ensures that at any given time, the entire network is either "On" or "Off."
*   **Analysis:** Aggregating metrics by time-window and comparing the "Treatment Windows" vs. "Control Windows."

## 2. Difference-in-Differences (DiD)
*   **Use Case:** You launched a new incentive program in City A, but not in City B.
*   **The Method:** Comparing the *change* in City A (Pre vs. Post) to the *change* in City B (Pre vs. Post).
*   **Assumptions:** "Parallel Trends"—assuming that without the treatment, City A would have followed the same trend as City B.

## 3. Synthetic Control Method
*   **Use Case:** Measuring the impact of a new government regulation on food delivery in a single city (e.g., Singapore).
*   **The Method:** Building a "Synthetic Singapore" by weighting other cities (Bangkok, Manila, Kuala Lumpur) to match Singapore's pre-regulation performance.
*   **Comparison:** Comparing the actual post-regulation performance to the "Synthetic" prediction.

## 4. Causal Impact Analysis (Python)
*   **Libraries:** Using `CausalML` or `EconML` to estimate heterogeneous treatment effects (finding which riders or zones benefited the most from a change).
*   **Propensity Score Matching:** Using statistical matching to create a control group from observational data when randomization wasn't possible.

## Interview Questions
1.  "Explain why a traditional A/B test might fail when testing a new rider-dispatch algorithm."
2.  "What is a 'Switchback Test' and what are the trade-offs in choosing the time-window duration (e.g., 30 mins vs. 2 hours)?"
3.  "How do you validate the 'Parallel Trends' assumption in a Difference-in-Differences (DiD) analysis?"
4.  "Walk me through how you would measure the causal impact of a 10% increase in rider pay on delivery speed using a Synthetic Control approach."
