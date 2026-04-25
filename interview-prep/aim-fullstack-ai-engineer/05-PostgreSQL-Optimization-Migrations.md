# PostgreSQL: Optimization & Migrations
**Focus:** Indexing Strategies, Zero-Downtime Migrations, N+1 Problems, Integration Testing

---

## Section A: Advanced PostgreSQL Operations

### Q1: Indexing Strategies for Complex Queries
**Question:** Describe your strategy for indexing PostgreSQL databases to optimize complex analytical queries, such as generating payroll reports across multiple tables.

**Expected Answer:**
Optimizing complex PostgreSQL queries requires empirical analysis, starting strictly with the `EXPLAIN ANALYZE` command to identify sequential scans and costly sorting operations. A simple B-Tree index on a single column is rarely sufficient for complex analytical reports. 

If a payroll query consistently filters on an agency ID and a specific date range, I implement a **Composite Index** on `(agency_id, shift_date)`. The order matters critically; the equality filter (`agency_id`) must precede the range filter (`shift_date`). For JSONB columns (common in modern apps), I utilize **GIN (Generalized Inverted Index)** to rapidly query key-value pairs inside the JSON payload. Furthermore, if a report frequently calculates sums (e.g., total hours worked), I would create a **Partial Index** (e.g., `CREATE INDEX ON shifts (employee_id) WHERE status = 'completed'`) to keep the index size drastically smaller and faster, or implement materialized views to pre-aggregate heavy analytical metrics entirely asynchronously.

---

### Q2: Zero-Downtime Schema Migrations
**Question:** How do you safely execute a schema migration (like dropping or renaming a column) on a massive production PostgreSQL database with zero downtime?

**Expected Answer:**
Executing a naive `ALTER TABLE RENAME COLUMN` on a live production database will result in application crashes, as active Node.js servers will instantly fail when querying the old column name. Zero-downtime migrations demand a multi-step **Expand and Contract** pattern.

Step 1 (Expand): Create a migration to add the *new* column. Deploy the code. The application now dual-writes to both the old and new columns, but reads only from the old. 
Step 2 (Migrate): Execute a background script to backfill historical data from the old column to the new column in small, non-locking batches. 
Step 3 (Transition): Deploy application code that reads from and writes exclusively to the new column. 
Step 4 (Contract): Finally, months later, once all risk has subsided, execute a final migration to safely `DROP` the old column. This guarantees that database schema changes are completely decoupled from application code deployments.

---

## Section B: ORM Optimization & Integrity

### Q3: Resolving the N+1 Query Problem
**Question:** What is the N+1 query problem, and how do you resolve it in a Node.js/PostgreSQL backend (using an ORM or Query Builder)?

**Expected Answer:**
The N+1 query problem is a performance killer occurring when an ORM fetches a list of records (1 query) and then executes an additional query for each record in a loop (N queries) to fetch related data. For example, fetching 50 Care Workers, and then looping through them to fetch their Assigned Shifts results in 51 separate database round-trips.

To resolve this in a Node.js environment using Prisma, TypeORM, or Knex, I strictly enforce **Eager Loading**. Instead of lazy-fetching in a loop, I instruct the ORM to fetch the relations immediately using an `include` or `relations` parameter. The underlying query builder resolves this efficiently by executing a `LEFT JOIN` or by performing exactly two queries (one for the workers, one for the shifts using a `WHERE worker_id IN (...)`) and stitching the results together in Node memory, drastically reducing network latency and database CPU load.

---

### Q4: Data Integrity and Constraints
**Question:** How do you ensure absolute data integrity using PostgreSQL constraints and transactions in a financial/payroll context?

**Expected Answer:**
In a payroll system, application-level validation in Node.js is insufficient because race conditions or manual database edits can bypass the code entirely. Absolute integrity must be enforced at the database level.

I heavily utilize **Constraints**: explicit `NOT NULL` constraints, `UNIQUE` constraints to prevent duplicate payroll entries for the same shift, and `CHECK` constraints (e.g., `CHECK (hourly_rate > 0)`) to mathematically prevent impossible financial states from ever being written to disk. For complex operations involving multiple tables (e.g., deducting an agency budget and crediting an employee ledger), I wrap the SQL statements in strict **ACID Transactions**. If the server crashes between the budget deduction and the ledger credit, the database guarantees the entire transaction is rolled back cleanly, preventing money from being created or destroyed into the ether.

---

### Q5: Real-Database Integration Testing
**Question:** Describe how you implement integration tests against a real PostgreSQL database rather than mocking the database layer.

**Expected Answer:**
Mocking a database is dangerous because mocks cannot replicate complex SQL behaviors, foreign key constraints, or exact ORM idiosyncrasies, leading to false-positive tests. For robust integration testing, I execute tests against a genuine, ephemeral PostgreSQL instance.

I use **Testcontainers** (or Docker Compose) to spin up a fresh, isolated PostgreSQL container programmatically before the test suite runs. The test setup script applies all database migrations to ensure the schema perfectly matches production. To ensure tests remain fast and deterministic without interfering with each other, I run each test block within a database transaction. At the end of the test, I execute a `ROLLBACK` instead of a `COMMIT`. This guarantees that the database is instantly wiped clean, providing a pristine state for the next test without the immense overhead of tearing down and rebuilding the entire schema between assertions.
