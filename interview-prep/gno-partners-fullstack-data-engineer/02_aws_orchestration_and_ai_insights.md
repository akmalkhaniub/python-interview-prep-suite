# Deep Dive: AWS Orchestration & AI Insights

GNO Partners wants to feed "clean, structured data" into LLMs for seller advice.

## 1. Orchestrating with AWS Step Functions
*   **The Workflow:**
    - **Step 1:** Lambda fetches the list of active Sellers.
    - **Step 2:** Map state triggers parallel ingestion for each Seller.
    - **Step 3:** Wait state polls Amazon for report readiness.
    - **Step 4:** Processing Lambda transforms and saves data.
    - **Step 5:** Final Lambda triggers the "AI Insight Generator".
*   **Benefits:** Visual debugging of failed steps and automatic retries without custom code.

## 2. Event-Driven Architecture (SQS/SNS)
*   **Use Case:** When an "Order Updated" event comes from Amazon, push to SNS.
*   **Subscribers:**
    - **Lambda A:** Updates the Inventory database.
    - **Lambda B:** Triggers an alert if a high-value item is sold.
    - **Lambda C:** Syncs the sale to an external accounting tool.

## 3. Preparing Data for AI Insights
*   **The Problem:** LLMs cannot process millions of raw database rows.
*   **The Solution (Aggregation):**
    - Calculate "ACOS (Advertising Cost of Sales)" by week.
    - Identify "Top 5 most profitable products" and "Top 5 least profitable."
    - Feed these *structured summaries* into the LLM prompt.
*   **Prompt Example:** "Based on this weekly summary (Sales: $10k, Ads: $4k, ACOS: 40%), suggest 3 ways to improve profitability."

## 4. Full-Stack Tooling (NestJS + React)
*   **NestJS Architecture:** Using `Services` to encapsulate the Amazon API logic so it can be reused by both the "Cron Jobs" and the "Manual Refresh Button" in the UI.
*   **React Real-time Updates:** Using WebSockets or simple polling to show the "Sync Progress" to the user while the backend pipeline is running.

## Interview Questions
1.  "How would you use AWS Step Functions to manage a complex 5-step Amazon report ingestion process?"
2.  "Describe your experience feeding structured data into an LLM. How do you decide what information to include and what to omit?"
3.  "What are the benefits of using an Event-Driven architecture (SQS/SNS) over a traditional monolithic Cron job?"
4.  "How do you handle 'Sync Conflicts' when a user tries to manually refresh a report while an automated background sync is already in progress?"
