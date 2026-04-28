# 03: Data Ingestion and Orchestration

Building robust pipelines from ERPs like Sage and Xero using Fabric Data Factory.

## 1. Fabric Data Factory
*   **Data Pipelines**: Orchestrating activities (Copy data, Notebooks, Stored Procs). Supports loops, conditions, and error handling.
*   **Dataflows Gen2**: Power Query-based ingestion. Good for business users but also powerful for technical cleaning. Outputs to Lakehouse or Warehouse.

## 2. Ingestion Patterns
*   **CDC (Change Data Capture)**: Capturing only the changed data from source databases. Minimizes load on source and target.
*   **SCD (Slowly Changing Dimensions)**:
    *   **Type 1**: Overwrite old data (Current state only).
    *   **Type 2**: Keep history (Add a new row with `ValidFrom` and `ValidTo` dates).
*   **Upsert Logic**: Using the `MERGE` statement in SQL or `deltaTable.merge()` in Spark to handle inserts and updates in one go.

## 3. Integrating ERPs (Sage, Xero)
*   **API-based Ingestion**: Using Web activities or Python notebooks to call Sage/Xero REST APIs.
*   **Authentication**: Managing OAuth tokens and secrets using Azure Key Vault (integrated with Fabric).
*   **Pagination**: Handling large data volumes by iterating through API pages.

## Interview Questions
1.  **"How do you implement an SCD Type 2 pattern using PySpark?"**
    *   *Ans*: I would use the Delta Lake `merge` operation. I compare incoming records with existing ones; if a change is detected in an attribute, I 'expire' the old record (ValidTo = Today) and insert a new record (ValidFrom = Today).
2.  **"What is the advantage of using Dataflows Gen2 over traditional Dataflows?"**
    *   *Ans*: Dataflows Gen2 can output directly to Fabric data stores (Lakehouse/Warehouse) and supports the "staging" feature to improve performance for large transformations.
3.  **"How would you build a pipeline that pulls from a REST API and handles rate limiting?"**
    *   *Ans*: I'd use a Notebook. I'd implement a loop with `try-except` blocks and `time.sleep()` logic, or check the `Retry-After` header in the API response.

## Practice Task
*   Write a pseudo-code for a Spark merge statement that handles an "Upsert" for an `Invoices` table based on `InvoiceID`.
