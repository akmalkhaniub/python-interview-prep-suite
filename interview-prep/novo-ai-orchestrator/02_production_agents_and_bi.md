# Deep Dive: Production Agents & BI Hypothesis Formation

Novo AI engineers build agents that watch themselves and agents that find "where the money is going."

## 1. The "Watchdog" Agent Pattern
*   **The Mission:** Prevent 2 AM wake-up calls by having agents monitor production.
*   **Self-Correction:** If an agent detects an anomaly (e.g., "Medical inflation in Thailand spiked 50% in one day"), it should:
    1.  Verify if the spike is real or a data ingestion bug.
    2.  Check the recent code deploys.
    3.  Generate a Slack alert with the *actual* cause, not just a stack trace.

## 2. Autonomous BI & Hypothesis Formation
*   **The Problem:** Insurance companies have massive databases but few insights.
*   **The Agentic Solution:**
    - Give the agent access to SQL tools and a schema description.
    - Ask a high-level question: "Why is our payout for maternity care in France increasing faster than inflation?"
    - **Self-Planning:** The agent writes the SQL, analyzes the results, forms a hypothesis (e.g., "Average hospital stay increased by 1.5 days"), and verifies it with more queries.

## 3. Real-World Impact: Cost & Fraud
*   **Fraud Detection Agents:** Building agents that can look for "Upcoding" patterns (charging for a more expensive procedure than was actually performed).
*   **Market Analysis:** Agents that monitor medical inflation across different countries (Singapore vs. France) to help insurers adjust their premiums dynamically.

## 4. Architecting for Agentic BI
*   **Tooling:** Ensuring the AI has the right "Tools" (Python sandbox, SQL client, Web search).
*   **Transparency:** Every step of the agent's "Thought Process" (CoT) must be logged so humans can audit the hypothesis.

## Interview Questions
1.  "How do you build a production agent that can distinguish between a 'Transient Flake' and a 'Systemic Bug'?"
2.  "Describe how you would design an agent to autonomously investigate a 15% increase in medical costs. What is its first query?"
3.  "What are the safety risks of an autonomous BI agent having write-access to a production database? How do you mitigate them?"
4.  "Walk me through your experience using Cursor and Claude together to build a complex system. How did you handle the hand-off between you and the AI?"
