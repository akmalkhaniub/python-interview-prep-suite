# Deep Dive: Statistical Rigor & Ad Fraud Detection

Pixalate protects businesses from multi-million dollar scams. Success here requires moving beyond "black box" ML to rigorous statistical proof.

## 1. The Statistics of Fraud
*   **The Baseline Problem:** In digital advertising, 95%+ of traffic might be legitimate. Fraud is the "needle in the haystack."
*   **Heavy Tails:** Fraudulent activity often appears as outliers in distributions of "Events per IP" or "Clicks per User."
*   **Benford's Law:** A common statistical tool used to detect if numerical data (like pricing or timestamps) has been artificially generated.

## 2. Detecting "Made For Advertising" (MFA)
*   **What is MFA?** Low-quality sites designed solely to host ads, often using deceptive traffic-acquisition tactics.
*   **Identification Levers:**
    - **Ad-to-Content Ratio:** Statistical analysis of page layout.
    - **Session Duration:** MFA sites often have high bounce rates or "fake" interaction signals.
    - **Traffic Sources:** Using hypothesis testing to compare the traffic origin of a site vs. its industry peers.

## 3. CTV (Connected TV) Scams
*   **Theroku/FireTV Vector:** Scammers create "fake apps" that simulate ad views in the background while the TV is off.
*   **Anomaly Detection:** Finding patterns of high-volume ad calls from a single device that don't align with human viewing behavior (e.g., watching TV 24 hours a day).

## 4. Model Evaluation & Calibration
*   **The Cost of a False Positive:** Flagging a legitimate publisher as "fraudulent" can destroy their business.
*   **Calibration:** Ensuring that a model's predicted probability (e.g., "80% chance of fraud") corresponds to the real-world frequency of fraud.
*   **Evaluation Metrics:** Focusing on **Partial AUC** or **Precision at fixed Recall** to minimize false accusations.

## Interview Questions
1.  "How do you distinguish between a 'Viral Event' (legitimate spike) and a 'Bot Attack' using statistical distributions?"
2.  "Describe your experience with Hypothesis Testing in an environment with non-Gaussian, noisy data."
3.  "What are the statistical pitfalls of using a standard Random Forest for fraud detection without addressing class imbalance?"
4.  "How would you prove to a client that a specific app is 'Spying' on children using behavioral signal analysis?"
