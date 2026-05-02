# 03 - Databases (SQL & NoSQL)

## SQL vs. NoSQL

### 1. When to choose PostgreSQL (SQL) over MongoDB (NoSQL)?
**Answer:**
- **PostgreSQL:** When data structure is complex, requires strict integrity (ACID), and has many relationships (Joins). Good for financial systems, ERPs.
- **MongoDB:** When data is unstructured/semi-structured, schema changes frequently, or you need to handle massive volumes of horizontal scaling (sharding). Good for real-time analytics, content management.

### 2. What is "Database Normalization"?
**Answer:**
The process of organizing data to minimize redundancy and dependency by splitting tables and using foreign keys. (1NF, 2NF, 3NF).

### 3. What are Indexes and why are they used?
**Answer:**
Data structures that improve the speed of data retrieval at the cost of storage space and slower writes. Use them on columns frequently used in `WHERE` or `JOIN` clauses.

## Modeling

### 4. How do you handle "One-to-Many" vs. "Many-to-Many" relationships?
**Answer:**
- **One-to-Many:** Add a foreign key to the "Many" side.
- **Many-to-Many:** Create a "Join Table" (or Junction Table) that contains foreign keys from both tables.

### 5. What is a "N+1 Query Problem"?
**Answer:**
Occurs when the code executes 1 query to fetch parent records and then N separate queries to fetch children for each parent. 
- **Fix:** Use "Eager Loading" (Joins or subqueries) to fetch everything in 1 or 2 queries.

### 6. Explain MongoDB "Aggregation Framework."
**Answer:**
A multi-stage pipeline used to process and transform documents. Stages include `$match` (filter), `$group` (aggregate), `$sort`, and `$project` (reshape).
