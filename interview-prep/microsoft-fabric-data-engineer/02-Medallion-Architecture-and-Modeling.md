# 02: Medallion Architecture and Modeling

A key requirement in the JD is designing and building the medallion architecture using Delta tables.

## 1. Medallion Architecture
*   **Bronze (Raw)**: Landing zone for data in its original format. Minimal transformation. Includes audit columns (IngestionTime, SourceFile).
*   **Silver (Validated/Cleaned)**: Data is cleaned, filtered, and joined. Schema is enforced. Deduping happens here.
*   **Gold (Curated/Business)**: Data is modeled for consumption (Star Schema). Contains aggregations and business logic. Ready for Power BI.

## 2. Delta Lake Optimization
*   **V-Order**: A Microsoft-proprietary optimization for Parquet files that makes reads significantly faster (especially for Power BI).
*   **Optimize & Vacuum**: `OPTIMIZE` compacts small files into larger ones; `VACUUM` removes old files no longer in the transaction log.
*   **Partitioning**: Choosing the right column (e.g., `DateID`) to prune data during queries. Avoid over-partitioning.

## 3. Dimensional Modeling
*   **Star Schema**: Fact tables joined to multiple Dimension tables.
*   **Conformed Dimensions**: Ensuring dimensions like `Date` or `Customer` are consistent across the entire enterprise.
*   **Snowflake Schema**: Normalized dimension tables (rarely used in Fabric unless necessary for specific normalization).

## Interview Questions
1.  **"What logic belongs in the Silver layer vs. the Gold layer?"**
    *   *Ans*: Silver is for technical cleaning (data types, null handling, deduping). Gold is for business logic (aggregations, calculating KPIs, formatting for BI).
2.  **"Explain 'V-Order' and why it matters in Fabric."**
    *   *Ans*: V-Order is a write-time optimization that reorders data within Parquet files to improve read performance for the SQL engine and Power BI's Direct Lake mode.
3.  **"How would you handle a schema change in a Delta table in the Silver layer?"**
    *   *Ans*: Delta supports schema evolution. You can use `.option("mergeSchema", "true")` in Spark to add new columns without failing the pipeline.

## Practice Task
*   Design a Star Schema for a "Finance" domain including a `Fact_Invoices` table and dimensions for `Dim_Customer`, `Dim_Date`, and `Dim_Account`.
