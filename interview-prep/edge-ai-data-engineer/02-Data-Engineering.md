# 02: Data Engineering Pipelines (dbt/GCP)

Edge needs reliable data pipelines to power both analytics and AI systems.

## 1. ETL vs. ELT
- **ETL (Extract, Transform, Load):** Transforming data before it hits the warehouse (Legacy).
- **ELT (Extract, Load, Transform):** Loading raw data into BigQuery first, then using **dbt** to transform it (Modern Standard).

## 2. dbt (Data Build Tool)
dbt allows you to write transformations in SQL while using software engineering best practices (version control, testing, documentation).
- **Models:** SQL files that define tables/views.
- **Macros:** Reusable snippets of code (like functions).
- **Tests:** Ensuring data quality (e.g., `unique`, `not_null`).

## 3. GCP Data Stack
- **BigQuery:** The heart of the data platform.
- **Cloud Storage:** For landing raw files (CSV, JSON).
- **Cloud Functions / Cloud Run:** For running Python scripts that extract data from APIs (Salesforce/HubSpot).
- **Composer (Airflow):** For orchestrating complex multi-step pipelines.

## 4. Data Modeling
- **Star Schema:** Fact tables (e.g., `fct_sales`) surrounded by Dimension tables (e.g., `dim_customers`).
- **One Big Table (OBT):** Denormalized tables often used for faster query performance in BigQuery.

## 5. Potential Interview Questions
1. **How do you handle "Incremental Loading" in dbt?**
   - *Answer:* Use the `incremental` materialization and a `unique_key`. dbt will only process records newer than the last run, saving cost and time.
2. **Explain "Reverse ETL" and why Edge might need it.**
   - *Answer:* Reverse ETL (using tools like Hightouch or Census) pushes data *from* the warehouse back *into* business tools like Salesforce or Slack. For example: "If a user hasn't logged in for 30 days, notify the account manager in Slack."
3. **How do you ensure data quality in a production pipeline?**
   - *Answer:* Implement dbt tests for schema validation, use freshness checks to ensure data isn't stale, and set up alerting in Slack/Email for pipeline failures.
