# Interview Q&A: Data Engineer — Architect (Hybrid)

### Q1: The Data Audit
**Question:** You've just joined. The first task is to audit our current data architecture. What is your 7-day plan?
**Answer:** 
- **Days 1-2 (Discovery):** Map the data lineage from source to dashboard. Identify all ingestion points (Airbyte, custom scripts) and the current BigQuery structure.
- **Days 3-4 (Technical Audit):** Analyze query logs for performance bottlenecks and cost spikes. Check for duplicate transformations and inconsistent naming conventions.
- **Days 5-6 (Business Translation):** Interview key stakeholders (Head of Data, Finance, Ops) to identify "Data Gaps"—what questions can they *not* answer today?
- **Day 7 (Proposal):** Present a prioritized list of "Quick Wins" (cost optimization) and "Strategic Shifts" (implementing medallion architecture or dbt).

### Q2: Business Translation
**Question:** A business stakeholder asks for a "Real-time Profitability Dashboard," but the source data only syncs once a day. How do you handle this?
**Answer:** This is a classic "Expectation vs. Constraint" scenario. I would explain that while we can build the dashboard today, the data "freshness" will be T+24 hours. I would then propose a tiered approach:
1.  **Phase 1:** Deliver the daily dashboard using existing pipelines.
2.  **Phase 2:** Audit the source system to see if we can implement **Streaming Ingestion** or high-frequency Cloud Functions to move closer to real-time.
It's about managing expectations while providing a technical roadmap.

### Q3: BigQuery Cost Optimization
**Question:** Our BigQuery bill doubled this month. How do you find out why and fix it?
**Answer:** I would immediately check the **Information Schema** (`JOBS_BY_PROJECT`) to identify the "Heavy Hitters"—specific users or automated jobs processing the most bytes.
- **Common Fixes:** 
    - Convert `SELECT *` queries to specific columns.
    - Implement Partitioning/Clustering on the most expensive tables.
    - Check if a scheduled dbt run is failing and retrying in an infinite loop.
    - Implement "Authorized Views" to prevent analysts from accidentally scanning raw tables.

### Q4: Ingestion Feasibility
**Question:** We want to ingest data from a new 3rd party travel API. How do you assess if this is feasible for our current pipeline?
**Answer:** I evaluate against 4 criteria:
1.  **Connectivity:** Does the API support webhooks, or do we need to poll it?
2.  **Schema Stability:** Is the response format consistent, or does it change without notice?
3.  **Volume:** How much data are we talking about? Will it overwhelm our Cloud Functions?
4.  **Value:** Does this data join with our existing "Customer" or "Booking" IDs?
If it doesn't join or has poor reliability, I advise against integration until the source data is improved.

### Q5: Medallion Architecture
**Question:** Why bother with "Silver" and "Gold" layers? Why not just query the "Raw" data directly in Looker?
**Answer:** Querying raw data directly leads to **"Technical Debt"** and **"Semantic Chaos."** 
- **Performance:** Raw data is often unpartitioned and slow.
- **Consistency:** If 10 different analysts write their own logic to calculate "Net Revenue" from raw data, you'll get 10 different numbers.
- **Cost:** You'll scan the same raw data repeatedly.
The Silver layer centralizes "The Truth," and the Gold layer optimizes it for "The Answer."

---

### Pro-Tips for Polani GT:
- **Mention Karachi Context:** Discuss your experience with local data challenges (e.g., inconsistencies in manual entries from local branches).
- **Ownership Mindset:** Don't wait for tickets; tell them you'll find the problems yourself.
- **SQL is Your Sword:** Be ready for a live SQL coding challenge focusing on window functions, JSON parsing, and CTEs.
