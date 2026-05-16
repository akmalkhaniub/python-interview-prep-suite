# BigQuery Architect Deep-Dive (Performance & Cost)

Polani GT specifically asks for BigQuery expertise. This guide covers the "Architectural" side of BQ that differentiates a mid/senior engineer from a junior analyst.

---

## 1. Medallion Architecture in BigQuery
*   **Bronze Layer (Raw):** Use **External Tables** (pointing to Cloud Storage) or BigQuery **Streaming Inserts** for landing. No transformations here.
*   **Silver Layer (Curated):** This is where **dbt** comes in. Cast types, handle nulls, and enforce schemas. This table MUST be partitioned (usually by `ingestion_timestamp` or `event_date`).
*   **Gold Layer (Reporting):** Denormalized tables or Materialized Views optimized for BI tools like Looker. Minimize joins at this stage to reduce query cost and latency.

## 2. Partitioning vs. Clustering (The Cost Savers)
*   **Partitioning:** Physically splitting the table by a column (e.g., `booking_date`).
    *   *Why:* BigQuery only scans the relevant partitions, drastically reducing the "Bytes Processed" (and thus the cost).
*   **Clustering:** Sorting the data within partitions based on one or more columns (e.g., `airline_code`, `customer_id`).
    *   *Why:* Excellent for filter/group-by queries. Unlike partitioning, clustering has no limit on the number of values.
*   **Architect's Choice:** "I always partition by time and cluster by the most frequent filter columns (like `status` or `branch_id`) to ensure sub-second response times for dashboards."

## 3. Cost Governance & Monitoring
*   **Dry Runs:** Always run a `dry_run` before executing a query to estimate cost.
*   **Maximum Bytes Billed:** Set this limit on user profiles to prevent "rogue queries" from eating the budget.
*   **Slots vs. On-Demand:**
    *   *On-Demand:* Pay per TB scanned ($6.25/TB). Good for sporadic workloads.
    *   *Capacity (Slots):* Dedicated processing power. Better for predictable, high-volume workloads like those at a major travel group.
*   **Talking Point:** "I audit query logs to identify the 'Top 10 Most Expensive Queries' and refactor them using CTEs or Materialized Views to optimize the company's GCP spend."

## 4. Materialized Views (MV)
*   **Concept:** Pre-computed results that BigQuery automatically keeps up-to-date.
*   **Use Case:** Aggregating "Daily Revenue by Airline" from a table with billions of rows.
*   **Talking Point:** "I use Materialized Views for reporting layers because they offer the performance of a static table with the freshness of a live view, and BigQuery's 'Smart Tuning' can use them even if the user queries the base table."

## 5. Schema Evolution & Governance
*   **Nested & Repeated Fields:** Use `STRUCT` and `ARRAY` instead of flattening everything. This reduces data redundancy and improves performance for complex travel records.
*   **Data Catalog:** Use Google Cloud Data Catalog (Dataplex) to tag sensitive columns (PII) and provide a searchable inventory for business users.
