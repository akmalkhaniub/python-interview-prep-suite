# Database Management

**Focus:** Relational (PostgreSQL, MySQL) vs Non-relational (MongoDB), Schema Design, ORMs, Performance

---

### Q1: SQL vs NoSQL (PostgreSQL vs MongoDB)
**Question:** The job requires experience with both relational (PostgreSQL/MySQL) and non-relational (MongoDB) databases. How do you choose which to use for a specific feature or project?

**Expected Answer:**
- **PostgreSQL / MySQL (Relational)**:
  - **Use when**: Data is highly structured, relationships are complex, ACID compliance is strictly required (e.g., financial transactions, billing systems), and queries involve complex joins.
  - **Pros**: Strong data integrity, predictable schema, powerful SQL querying.
- **MongoDB (Non-relational / Document)**:
  - **Use when**: Data is unstructured or semi-structured, schema changes frequently, rapid prototyping is needed, or handling massive volumes of read-heavy data (e.g., user profiles, catalogs, logging, IoT data).
  - **Pros**: Flexible schema, horizontal scalability (sharding), fast read performance for document-centric access patterns.

---

### Q2: Schema Design in MongoDB
**Question:** In MongoDB, how do you decide whether to embed a document or reference it?

**Expected Answer:**
- **Embed (Denormalization)**:
  - Use when the entities have a "contains" relationship (1-to-few), and the embedded data is frequently accessed *together* with the parent document.
  - *Example*: An `Order` document embedding `LineItems` or `ShippingAddress`.
  - *Pros*: Faster read performance (single query).
  - *Cons*: Document size limits (16MB), harder to update if the embedded data needs to change across multiple parents.
- **Reference (Normalization)**:
  - Use when the entities have a many-to-many relationship, or a 1-to-many where the "many" is very large (unbounded).
  - *Example*: A `User` document referencing `Post` IDs, or a `Book` referencing an `Author`.
  - *Pros*: Avoids duplication, keeps documents small.
  - *Cons*: Requires multiple queries or `$lookup` (aggregations), which is slower.

---

### Q3: Database Transactions
**Question:** Explain how transactions work in a relational database like PostgreSQL. Does MongoDB support transactions?

**Expected Answer:**
- **PostgreSQL**: Transactions group multiple operations into a single logical unit of work following ACID properties (Atomicity, Consistency, Isolation, Durability). Either all operations succeed (Commit), or none do (Rollback).
- **Use case**: Transferring money from Account A to Account B.
- **MongoDB**: Yes, MongoDB supports multi-document ACID transactions (introduced in v4.0). However, because MongoDB is designed for denormalized data models (embedding), transactions should be used sparingly as they incur a performance cost compared to single-document updates (which are atomic by default).

---

### Q4: Database Indexing & Performance
**Question:** A query on your PostgreSQL database that used to take 50ms is now taking 3 seconds as the table has grown to 5 million rows. How do you optimize it?

**Expected Answer:**
- **Analysis**: Use `EXPLAIN ANALYZE` to see the query execution plan. Look for "Sequential Scans" (Seq Scan), which indicate the database is scanning every row.
- **Indexing**: Add a B-Tree index on the columns used in the `WHERE`, `JOIN`, or `ORDER BY` clauses.
- **Composite Indexes**: If querying by multiple columns frequently (e.g., `WHERE status = 'active' AND type = 'premium'`), create a composite index. Order matters: put the most selective column first.
- **Trade-offs**: Indexes speed up `SELECT` queries but slow down `INSERT`, `UPDATE`, and `DELETE` operations because the index must be updated. Don't over-index.
- **N+1 Query Problem**: If using an ORM, ensure related data is fetched using joins/includes rather than looping through records and executing a query for each.

---

### Q5: ORMs vs ODMs vs Query Builders
**Question:** In Node.js, what are the differences between using Mongoose, Prisma, and Knex.js?

**Expected Answer:**
- **Mongoose (ODM for MongoDB)**: Provides schema validation, middleware, and type casting on top of MongoDB's schema-less nature.
- **Prisma (Next-gen ORM)**: Highly type-safe (generates TypeScript types from the schema). Uses a custom schema file. Great developer experience, but abstraction can sometimes obscure complex SQL performance issues.
- **Knex.js (Query Builder)**: Lower level than an ORM. You write JS that closely resembles SQL. Excellent for complex queries where ORMs generate inefficient SQL, while still being safer and more dynamic than writing raw SQL strings.
