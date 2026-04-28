# 01: Fabric Core Architecture

Microsoft Fabric is an all-in-one analytics solution that covers everything from data movement to data science, Real-Time Analytics, and business intelligence.

## 1. OneLake (The "OneDrive for Data")
*   **Single Store**: OneLake is a single, unified, logical data lake for the entire organization.
*   **Shortcuts**: Ability to reference data in other locations (ADLS Gen2, AWS S3, Google Storage) without moving it.
*   **Open Format**: Data is stored in Delta Lake (Parquet) format, making it accessible to any engine.

## 2. Lakehouse vs. Data Warehouse
| Feature | Lakehouse | Data Warehouse |
|---|---|---|
| **Primary Engine** | Spark | T-SQL |
| **Schema** | Schema-on-read | Schema-on-write |
| **Files** | Delta Tables / Folders | Tables (Internal storage) |
| **Best For** | Big data, Data Science, Python/Spark | Structured SQL, BI Reporting, ACID transactions |
| **Commonality** | Both store data in OneLake as Delta Lake files. | |

## 3. Capacity Management
*   **Capacity Units (CU)**: The compute power assigned to a Fabric workspace.
*   **Workload Isolation**: Ensuring heavy Spark jobs don't starve BI reports of compute resources.
*   **Bursting & Smoothing**: Fabric allows for temporary compute spikes (bursting) and spreads the cost over time (smoothing).

## Interview Questions
1.  **"What is the significance of 'Shortcuts' in OneLake?"**
    *   *Ans*: Shortcuts allow you to virtualize data. You can have data sitting in an AWS S3 bucket but treat it as if it's in your Fabric Lakehouse, avoiding data duplication and ETL complexity.
2.  **"When would you choose a Lakehouse over a Data Warehouse in Fabric?"**
    *   *Ans*: Use a Lakehouse if the workload is Spark-heavy, requires Python, or involves unstructured data. Use a Warehouse if the team is SQL-centric and needs full T-SQL support for complex DML operations.
3.  **"How does 'One Copy' differ from traditional data silos?"**
    *   *Ans*: 'One Copy' means all engines (Spark, SQL, Power BI) read from the same Delta Parquet files in OneLake. You don't need to copy data from the lake to the warehouse for reporting.

## Practice Task
*   Compare the performance of a 10M row join in a Lakehouse (using Spark) vs. a Data Warehouse (using SQL) in your lab environment.
