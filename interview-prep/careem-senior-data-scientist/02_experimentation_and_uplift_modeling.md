# Deep Dive: Experimentation & Uplift Modeling

Measuring the true impact of AI models in a complex marketplace requires advanced experimental techniques.

## 1. Uplift Modeling (Causal Inference)
*   **The Problem:** Standard predictive models tell you who *will* buy. Uplift models tell you who will buy *because* of a treatment (like a 10% discount).
*   **The Four Quadrants:**
    - **Sure Things:** Users who buy regardless of the discount (don't waste money).
    - **Lost Causes:** Users who won't buy regardless (don't waste money).
    - **Sleeping Dogs:** Users who might be *annoyed* by a discount/notification (avoid).
    - **Persuadables:** Users who only buy if they get the discount (Focus here).
*   **Methods:** Using S-Learner, T-Learner, or X-Learner meta-algorithms.

## 2. Experimental Design in Marketplaces
*   **The Interference Problem:** If you give a discount to half the users in a city, the other half might see longer wait times because the first half is "using up" the supply. This violates the SUTVA (Stable Unit Treatment Value Assumption).
*   **Switchback Testing:** Toggling the treatment (A or B) for an entire region over time windows (e.g., 30-minute intervals).
*   **Cluster-based Randomization:** Randomizing groups of hexagons or cities to minimize spillover effects.

## 3. Product Analytics & Growth
*   **Retention Analysis:** Identifying the "Aha! moment" for new Careem users (e.g., "completed 3 rides in 7 days").
*   **Funnel Optimization:** Using data to find where users drop off in the "Everything App" journey (from ride selection to payment).

## 4. Scalable EDA (BigQuery/SQL)
*   **Cohort Analysis:** Segmenting users by their acquisition date to track long-term value (LTV).
*   **Anomaly Detection:** Detecting sudden shifts in key metrics (like conversion rate) and identifying if it's a technical bug, a holiday, or a competitor's campaign.

## Interview Questions
1.  "Explain the difference between a Propensity Model and an Uplift Model. When would you use one over the other?"
2.  "How do you design a Switchback Test for a new ride-hailing feature in Dubai? What are the potential biases?"
3.  "Describe a time you discovered a 'Root Cause' for a drop in a primary metric. What data did you use for your analysis?"
4.  "What are the most important KPIs for a 'Super App' like Careem, and how do you balance conflicting metrics (e.g., GMV vs. Customer Satisfaction)?"
