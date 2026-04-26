# 04: MSSQL & Performance Tuning

For a Full Stack role at 10Pearls, understanding the "Data" layer is as important as the "Logic" layer.

## 1. Query Optimization
- **Execution Plans:** Using SQL Server Management Studio (SSMS) to see where the bottlenecks are.
- **Index Seek vs. Index Scan:** Seek is targeted (fast); Scan reads the whole index (slow).
- **Avoiding `SELECT *`:** Only fetching the columns you need to reduce I/O.

## 2. Indexing Strategies
- **Clustered Index:** The physical order of data on disk (usually the Primary Key). Only one per table.
- **Non-Clustered Index:** A separate structure that points to the data. Multiple allowed.
- **Filtered Indexes:** Indexing only a subset of data (e.g., `WHERE IsActive = 1`).

## 3. Database Design
- **Normalization (1NF, 2NF, 3NF):** Reducing redundancy.
- **Denormalization:** Sometimes necessary for high-performance read-heavy scenarios.
- **Foreign Keys & Constraints:** Ensuring data integrity.

## 4. Entity Framework Core (EF Core)
- **Lazy Loading vs. Eager Loading:** Eager loading (`.Include()`) prevents the N+1 problem.
- **AsNoTracking():** Speeding up read-only queries by telling EF not to track changes.
- **Migrations:** Version-controlling your database schema.

## 5. Potential Interview Questions
1. **How do you find the slowest query in a SQL Server database?**
   - *Answer:* Use **Query Store** or Dynamic Management Views (DMVs) like `sys.dm_exec_query_stats`.
2. **What is "Parameter Sniffing"?**
   - *Answer:* When SQL Server creates an execution plan based on the parameters of the *first* run, which might not be optimal for subsequent runs with different parameters.
3. **Difference between `INNER JOIN` and `LEFT JOIN`.**
   - *Answer:* `INNER JOIN` returns only rows with matches in both tables. `LEFT JOIN` returns all rows from the left table and matching rows from the right (or NULL if no match).
4. **How do you handle "Transactions" across multiple services?**
   - *Answer:* Using the **Saga Pattern**. You can't use distributed transactions (2PC) easily in microservices. A Saga coordinates a sequence of local transactions, with "compensating transactions" to roll back if one fails.
