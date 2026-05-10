# Deep Dive: Amazon API & Data Pipelines

GNO Partners is moving from manual uploads to direct Amazon API integrations. This transition requires extreme resilience and database optimization.

## 1. Navigating Amazon SP-API
*   **The Ingestion Flow:**
    1.  **Schedule:** Trigger a `createReport` request.
    2.  **Poll:** Periodically check `getReport` status until 'DONE'.
    3.  **Download:** Fetch the report document (often compressed GZIP).
    4.  **Parse:** Extract and normalize the data into the Postgres schema.
*   **Rate Limit Management:** Amazon uses strict throttling. Your pipeline must include a global "Leaky Bucket" or "Fixed Window" limiter to prevent 429 errors.

## 2. Postgres Warehouse Optimization
*   **The Problem:** Storing millions of rows for hundreds of different sellers (multi-tenancy).
*   **The Strategy:**
    - **Row-Level Security (Supabase/Postgres):** Ensuring Seller A cannot see Seller B's data.
    - **Partial Indexing:** If 90% of your queries are for "Active" inventory, create an index `WHERE status = 'active'`.
    - **Materialized Views:** Pre-calculating complex "Monthly Profitability" metrics so the React dashboard loads in < 500ms.

## 3. Handling "Messy" Vendor Data
*   **Schema Evolution:** Amazon adds or removes fields from reports without warning. Use JSONB for "Extra Fields" while keeping core metrics (Price, SKU, Qty) in typed columns.
*   **Data Backfills:** When a new client joins, the pipeline needs to fetch 6-12 months of historical data without overloading the system.

## 4. Idempotency & Correctness
*   **The Goal:** If a Lambda function retries a task, it shouldn't count the same sale twice.
*   **Implementation:** Use a `UNIQUE` constraint on `(seller_id, amazon_order_id)` and use `ON CONFLICT DO UPDATE` (Upsert).

## Interview Questions
1.  "How would you design a retry strategy for an Amazon API call that fails with a 429 (Too Many Requests) vs. a 500 (Internal Server Error)?"
2.  "Describe your approach to partitioning a Postgres database that stores 100M+ Amazon transactions."
3.  "Walk me through the architecture of an incremental sync pipeline that keeps inventory levels up to date every 15 minutes."
4.  "How do you ensure data quality when the source API (Amazon) provides inconsistent or null values for critical fields?"
