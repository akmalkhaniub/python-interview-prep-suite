# Database Design & MySQL Optimization
**Focus:** Relational Schema Design, Indexing, Query Optimization, ACID Properties

---

## Section A: Schema Design & ACID

### Q1: Designing Normalized Relational Schemas
**Question:** Walk through your thought process for designing a relational database schema for a new e-commerce product catalog.

**Expected Answer:**
My process begins with strict **Entity-Relationship Modeling** to ensure data integrity and avoid redundancy, targeting Third Normal Form (3NF). First, I identify the core entities: `Products`, `Categories`, and `Attributes`. Because a product can belong to multiple categories, I avoid storing comma-separated IDs and instead design a bridging table (`ProductCategories`) to establish a clean Many-to-Many relationship using foreign keys with cascading constraints.

For product attributes (like size or color), e-commerce presents a challenge: attributes vary wildly between products (a TV has resolution, a shirt has size). Rather than creating a table with 50 nullable columns (which is a massive anti-pattern), I utilize the **Entity-Attribute-Value (EAV)** model or, more practically in modern MySQL (8.0+), a structured `JSON` column to store dynamic schemaless data while maintaining strict relational integrity for core searchable fields like `Price` and `SKU`. Finally, I strictly define data types—using `DECIMAL` for currency to prevent floating-point math errors, and appropriately sizing `VARCHAR` fields to optimize storage space.

---

### Q2: ACID Properties in MySQL
**Question:** What are the ACID properties, and how does MySQL (InnoDB) guarantee them?

**Expected Answer:**
ACID represents the fundamental guarantees of reliable database transactions. **Atomicity** ensures an "all or nothing" operation. If a transaction fails halfway, InnoDB rolls back all changes using the Undo Log. **Consistency** guarantees that the database transitions from one valid state to another, strictly enforcing foreign keys, unique constraints, and data types to prevent corruption.

**Isolation** ensures concurrent transactions do not interfere with each other. InnoDB provides different isolation levels, defaulting to `REPEATABLE READ`. It uses Multi-Version Concurrency Control (MVCC) to provide consistent snapshot reads, meaning one transaction's read is unaffected by another transaction's write. Finally, **Durability** ensures that once a transaction is committed, it survives system crashes. InnoDB achieves this by writing all transactions to the Write-Ahead Log (Redo Log) on disk before modifying the actual data files, allowing recovery of committed data upon reboot.

---

## Section B: Optimization & Indexing

### Q3: Optimizing Slow MySQL Queries
**Question:** A critical MySQL query powering an API endpoint is suddenly taking 5 seconds to execute. How do you troubleshoot and optimize it?

**Expected Answer:**
My first step is absolute empirical analysis using the `EXPLAIN` (and `EXPLAIN ANALYZE` in newer versions) command on the exact problematic query. I look specifically for the `type` column; if I see `ALL` or `index`, it indicates a devastating Full Table Scan or Full Index Scan. I check the `rows` column to see how many records the engine estimates it must examine, and the `Extra` column for red flags like `Using filesort` or `Using temporary`, which indicate expensive in-memory or on-disk sorting operations.

Based on the `EXPLAIN` output, the most common fix is introducing a proper **Composite Index**. If the query filters `WHERE status = 'active' AND created_at > '2023-01-01'`, an index on just `status` might not be selective enough. I would create a composite index on `(status, created_at)`. If the query is performing complex `JOIN`s, I verify that foreign key columns are explicitly indexed. If the table has grown to hundreds of millions of rows, indexing might not suffice, and I would evaluate architectural solutions like application-level caching (Redis), denormalization, or horizontal partitioning/sharding.

---

### Q4: Clustered vs. Non-Clustered Indexes
**Question:** Explain the difference between clustered and non-clustered (secondary) indexes in MySQL's InnoDB storage engine.

**Expected Answer:**
In InnoDB, a **Clustered Index** strictly dictates the physical storage order of the data on the disk. Every InnoDB table has exactly one clustered index, which is typically the Primary Key. The "leaf nodes" of the clustered index B-Tree contain the actual, entire row data. When you query by the Primary Key, the database traverses the B-Tree and instantly retrieves the full row from disk in a single step, making it incredibly fast.

A **Non-Clustered Index** (or Secondary Index) is created on any other column (e.g., `email` or `status`). The leaf nodes of a secondary index do *not* contain the row data. Instead, they contain the indexed value and a "pointer" back to the clustered index (the Primary Key value). Therefore, querying by a secondary index requires two steps: a lookup in the secondary index to find the Primary Key, followed by a second lookup in the clustered index to fetch the row data. To optimize this, if a query only `SELECT`s columns that are entirely contained within the secondary index, InnoDB can perform an "Index-Only Scan" (Covering Index), bypassing the clustered index lookup entirely and vastly improving performance.

---

### Q5: Zero-Downtime Schema Migrations
**Question:** How do you execute a massive schema migration (like adding a column to a 50-million row table) with zero application downtime?

**Expected Answer:**
Historically, running an `ALTER TABLE` in MySQL locked the table, completely blocking all `INSERT` and `UPDATE` operations and bringing the application down. While modern MySQL (8.0+) supports some instant DDL operations for basic tasks like appending columns, complex changes or operations on older versions require specialized strategies.

For massive tables, I employ the **Online DDL** capabilities or utilize industry-standard tools like `gh-ost` (GitHub's Online Schema Migrations) or `pt-online-schema-change` (Percona). These tools work by creating an empty, "ghost" copy of the target table with the new schema applied. They then slowly copy the historical data in small, non-blocking chunks from the original table to the ghost table. Simultaneously, they capture any live, incoming writes occurring on the original table using triggers or the binary log, and replay them onto the ghost table. Once the data is perfectly synchronized, the tool executes an atomic rename operation, swapping the ghost table into production instantly, achieving a massive migration with absolute zero downtime.
