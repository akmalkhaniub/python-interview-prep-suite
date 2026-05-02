# 03 - Database Design & Optimization

## Schema Design

### 1. Difference between Normalization and Denormalization?
**Answer:**
- **Normalization:** Minimizing redundancy by splitting data into multiple related tables (e.g., 3NF). Good for write-heavy systems.
- **Denormalization:** Intentionally adding redundancy to improve read performance (fewer joins). Common in read-heavy systems or data warehouses.

### 2. How do you design for "Data Integrity"?
**Answer:**
Using database constraints:
- **Primary Keys:** Unique identifier for each row.
- **Foreign Keys:** Ensuring relationships between tables are valid.
- **Check Constraints:** Ensuring values meet specific criteria (e.g., `age > 0`).
- **Transactions:** Ensuring all-or-nothing operations (ACID).

## Query Optimization

### 3. How do Indexes work?
**Answer:**
A data structure (usually a B-Tree) that allows the database to find rows much faster than a full table scan. 
- **Cost:** Indexes slow down writes (INSERT/UPDATE) and take up extra storage.

### 4. What is a "Composite Index"?
**Answer:**
An index on multiple columns (e.g., `last_name`, `first_name`). It is most effective when queries filter by the leftmost column first.

### 5. How to optimize a "Join" query?
**Answer:**
- Ensure the columns being joined are indexed.
- Filter the data as much as possible *before* the join (using `WHERE`).
- Avoid joining too many tables in a single query.

### 6. Relational (Postgres) vs. Document (MongoDB) for Backend?
**Answer:**
- **Relational:** Best for structured data with complex relationships and strict consistency.
- **Document:** Best for unstructured data, rapid schema changes, and high horizontal scale.
