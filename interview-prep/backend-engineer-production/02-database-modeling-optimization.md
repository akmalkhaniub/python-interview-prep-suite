# 02 - Database Modeling & Optimization

## Data Modeling

### 1. When to use a Relational (SQL) vs. Non-Relational (NoSQL) database?
**Answer:**
- **SQL (Postgres/MySQL):** When data has high integrity requirements, complex relationships (Joins), and a stable schema.
- **NoSQL (MongoDB/Cassandra/DynamoDB):** When data is unstructured, requires massive horizontal scale, or has a flexible schema that changes frequently.

### 2. Explain the "ACID" properties.
**Answer:**
- **Atomicity:** All or nothing (transactions).
- **Consistency:** Data follows all rules (constraints).
- **Isolation:** Concurrent transactions don't interfere.
- **Durability:** Once committed, data stays committed even after a crash.

### 3. What is "Database Normalization" vs. "Denormalization"?
**Answer:**
- **Normalization:** Reducing redundancy by splitting tables (e.g., 3NF). Good for write-heavy apps.
- **Denormalization:** Combining tables to reduce Joins. Good for read-heavy apps or analytical workloads (OLAP).

## Optimization

### 4. How do you optimize a "Slow Query"?
**Answer:**
1. **Explain Analyze:** Look at the query plan.
2. **Indexing:** Add indexes on columns used in `WHERE`, `JOIN`, or `ORDER BY`.
3. **Selectivity:** Only fetch the columns you need (`SELECT *` is bad).
4. **Caching:** Move frequent, static queries to Redis.
5. **Partitioning:** Split large tables into smaller pieces (e.g., by date).

### 5. What are "Database Migrations"?
**Answer:**
Version control for your database schema. It allows you to apply and roll back changes (adding columns, changing types) in a safe, repeatable way across different environments.

### 6. Importance of "Connection Pooling"?
**Answer:**
Managing a cache of database connections so that they can be reused. Opening/closing a connection for every request is expensive and can overwhelm the database. Tools like **PgBouncer** or language-specific libraries (e.g., `SQLAlchemy` pool) are essential.
