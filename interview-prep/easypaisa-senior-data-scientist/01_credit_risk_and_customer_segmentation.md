# Deep Dive: Credit Risk & Customer Segmentation

easypaisa operates as a digital bank, making real-time credit decisions for millions of users.

## 1. Credit Scoring for the Digital-First
*   **Alternative Data:** In Pakistan, many users are unbanked. Credit models must leverage **Telco data** (recharge patterns, data usage), **Utility payments**, and **In-app behavior** (frequency of wallet use).
*   **The Scorecard:** Building a points-based system that ranks users from "High Risk" to "Low Risk" to determine loan eligibility and credit limits.

## 2. Probability of Default (PD) Modeling
*   **Definition:** The likelihood that a borrower will fail to make full and timely repayments of their loan.
*   **Features:**
    - **Current Debt-to-Income:** (Estimated from wallet inflows).
    - **Historical Repayment:** Speed of previous loan settlements.
    - **Stability:** "Time since first app install" as a proxy for digital stability.
*   **Validation:** Using **Gini Coefficient** and **KS (Kolmogorov-Smirnov) Statistics** to measure the model's discriminatory power.

## 3. Behavioral Customer Segmentation
*   **Clustering (K-Means):**
    - **The Transactors:** Use the app for bill payments and mobile top-ups daily.
    - **The Savers:** Maintain a high balance but low transaction frequency.
    - **The Borrowers:** High propensity to take "Nano-loans" (Quick-Cash).
*   **Impact:** Tailoring app notifications and discount offers based on the specific cluster a user falls into.

## 4. Product Affinity (Association Rule Mining)
*   **The Insight:** "Users who pay their electricity bill in the app are 3x more likely to adopt the Health Insurance product."
*   **Implementation:** Using the **Apriori** algorithm to identify these "Rules" and feeding them into the marketing engine for personalized cross-selling.

## Interview Questions
1.  "How would you handle 'Missing Data' when building a credit model for a user who just downloaded the easypaisa app yesterday?"
2.  "Describe your experience with PD Modeling. What metrics do you use to ensure the model is 'Fair' and 'Discriminating'?"
3.  "Walk me through how you would use K-Means clustering to identify 'High-Value' customers for a new premium bank account launch."
4.  "What are the challenges of using ML for credit decisions in a country with high inflation and economic volatility?"
