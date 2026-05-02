# 03 - PostgreSQL & Data Modeling

## Relational Design

### 1. What is Normalization? Explain 1NF, 2NF, 3NF.
**Answer:**
Organizing data to reduce redundancy and improve integrity.
- **1NF:** Atomic values, no repeating groups.
- **2NF:** 1NF + all non-key attributes are fully dependent on the primary key.
- **3NF:** 2NF + no transitive dependencies (non-key attributes shouldn't depend on other non-key attributes).

### 2. Difference between INNER JOIN, LEFT JOIN, and FULL JOIN?
**Answer:**
- **INNER JOIN:** Returns only rows where there is a match in both tables.
- **LEFT JOIN:** Returns all rows from the left table and matched rows from the right. Unmatched right rows are NULL.
- **FULL JOIN:** Returns all rows when there is a match in either the left or right table.

## Performance & Scaling

### 3. What are Indexes and how do they work?
**Answer:**
Data structures (usually B-Trees in Postgres) that allow for faster retrieval of rows.
- **Tradeoff:** Speeds up SELECTs but slows down INSERTs/UPDATEs/DELETEs because the index must also be updated.
- **Composite Index:** An index on multiple columns. Column order matters (Prefix rule).

### 4. What is EXPLAIN ANALYZE?
**Answer:**
A command used to show the execution plan of a query.
- **EXPLAIN:** Shows the plan.
- **ANALYZE:** Executes the query and shows the *actual* runtimes and row counts. Crucial for finding slow sequential scans or inefficient joins.

### 5. How do you handle database migrations?
**Answer:**
Using tools like **Prisma**, **TypeORM**, or **Knex**. Migrations allow you to version-control the database schema changes and apply them consistently across dev/staging/prod environments.

### 6. ACID Properties?
**Answer:**
- **Atomicity:** All or nothing (Transaction).
- **Consistency:** Database moves from one valid state to another.
- **Isolation:** Concurrent transactions don't interfere.
- **Durability:** Committed data is permanent (survives crashes).
