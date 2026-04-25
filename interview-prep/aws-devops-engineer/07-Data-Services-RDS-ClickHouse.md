# Data Services: RDS PostgreSQL & ClickHouse

**Focus:** RDS Management, PostgreSQL Ops, ClickHouse, Database DevOps

---

## Section A: RDS PostgreSQL Operations

### Q1: RDS Architecture Decisions
**Question:** Walk through your decisions for a production PostgreSQL deployment: Multi-AZ, Read Replicas, instance sizing, storage.

**Expected Answer:**
For a production PostgreSQL deployment on RDS, my baseline architecture starts with enabling Multi-AZ deployments. This provides a synchronous standby replica in a different Availability Zone, ensuring zero data loss and automatic failover (typically within 60-120 seconds) in the event of hardware failure. If the application is read-heavy, I would supplement this by provisioning asynchronous Read Replicas to offload `SELECT` queries, which can also be deployed cross-region for Disaster Recovery (DR) purposes.

For compute, I default to the memory-optimized AWS Graviton instance families, such as `db.r6g.large`, because PostgreSQL heavily relies on caching, and Graviton offers superior price-performance. For storage, I strongly recommend `gp3` volumes over `gp2` because they allow you to scale IOPS and throughput independently of storage capacity, providing a baseline of 3,000 IOPS; I would only step up to `io2` Block Express for extreme, latency-sensitive transactional workloads. Finally, encryption at rest via AWS KMS must be enabled at creation time, and I would immediately attach a custom Parameter Group to tune PostgreSQL-specific settings like `shared_buffers`, `work_mem`, and `max_connections` to match the instance's memory footprint.

---

### Q2: Backup & Recovery
**Question:** Production RDS is corrupted by a bad migration. How do you recover?

**Expected Answer:**
Recovering from a catastrophic database corruption requires a precise understanding of RDS backup mechanisms. AWS handles automated backups by taking a daily storage volume snapshot and continuously archiving transaction logs (WAL files), which I typically configure with a 35-day retention policy. To recover from a bad migration, I would utilize Point-in-Time Recovery (PITR). PITR allows me to rewind the database to the exact second before the migration corrupted the schema. 

Crucially, PITR does not overwrite the existing corrupted database; it provisions an entirely new RDS instance. The recovery process involves executing the PITR restore, verifying the data integrity on the new instance, promoting it to production, and then meticulously updating the application's DNS records or connection strings to point to the new endpoint. Because PITR relies on continuous WAL streaming, the Recovery Point Objective (RPO) is typically less than 5 minutes. However, the Recovery Time Objective (RTO) can take hours depending on the total storage size of the database. To mitigate this during planned migrations, I always take an explicit manual snapshot beforehand, as they are not subject to automated deletion policies and provide a faster, absolute fallback point.

---

### Q3: Performance Troubleshooting
**Question:** App latency spikes — you suspect the database. How do you diagnose?

**Expected Answer:**
When diagnosing database-driven latency spikes, my first stop is the AWS RDS Performance Insights dashboard. I analyze the "Top SQL" queries sliced by "Wait Events" to understand exactly what the database is struggling with. For example, a spike in `IO:DataFileRead` indicates the database is going to disk too often, suggesting insufficient RAM or IOPS, while `Lock:transactionid` points directly to severe row-level lock contention between competing transactions. A spike in raw `CPU` waits usually highlights unoptimized queries.

Within PostgreSQL itself, I rely heavily on the `pg_stat_statements` extension to identify historical slow queries by their total execution time and mean duration. If I observe massive `Client:ClientRead` wait events, it strongly suggests connection exhaustion, requiring the immediate implementation of a connection pooler like PgBouncer or AWS RDS Proxy. Furthermore, I routinely check `pg_stat_user_tables` to monitor the accumulation of "dead tuples." If the autovacuum daemon is misconfigured or unable to keep up, table bloat will silently destroy query performance. Finally, any slow query identified must be manually dissected using `EXPLAIN ANALYZE` to ensure proper index utilization.

---

## Section B: Database DevOps

### Q4: Schema Migrations in CI/CD
**Question:** How do you safely run migrations on production PostgreSQL via GitLab CI?

**Expected Answer:**
Running schema migrations safely against a production PostgreSQL database via a CI/CD pipeline requires utilizing specialized tools like Alembic or Flyway and strictly adhering to zero-downtime deployment patterns. The migration must execute as a distinct, isolated CI stage *before* the application code is deployed. To prevent the migration from hanging the database, all DDL (Data Definition Language) operations must be non-locking. In PostgreSQL, this means rigorously using `CREATE INDEX CONCURRENTLY` and ensuring that new columns are added as `NULLABLE`, which makes the operation virtually instantaneous in Postgres 11+.

To handle breaking changes, the team must implement the "Expand-Contract" pattern spanning multiple deployment cycles. First, the schema is expanded (e.g., adding a new column). Second, the application is deployed to dual-write to both old and new schemas. Third, a background worker backfills historical data. Fourth, the application switches to exclusively reading the new schema. Finally, the old schema is dropped. From a security perspective, the GitLab CI runner executing this pipeline must run securely within a private VPC subnet, dynamically fetching the elevated database credentials from AWS Secrets Manager at runtime.

---

### Q5: RDS Proxy
**Question:** Why use RDS Proxy in front of PostgreSQL?

**Expected Answer:**
AWS RDS Proxy solves a fundamental architectural clash between serverless compute and traditional relational databases. PostgreSQL utilizes a "process-per-connection" model, meaning every connection consumes significant RAM and CPU overhead. If you have an AWS Lambda function that scales to 1,000 concurrent invocations, it will attempt to open 1,000 simultaneous database connections, instantly overwhelming and crashing the PostgreSQL instance. 

RDS Proxy sits between the application and the database, acting as a highly available connection pooler. It multiplexes thousands of incoming application requests across a small, warm pool of established database connections. Furthermore, RDS Proxy significantly improves resilience; during a Multi-AZ database failover, the proxy absorbs the connection errors and seamlessly queues incoming requests until the standby replica is promoted, reducing perceived application downtime from 60 seconds to roughly 20 seconds. Finally, it natively integrates with AWS Secrets Manager, allowing applications to use IAM authentication to connect to the proxy without ever needing to handle raw database passwords.

---

## Section C: ClickHouse

### Q6: ClickHouse vs. PostgreSQL
**Question:** When would you choose ClickHouse over PostgreSQL?

**Expected Answer:**
Choosing between ClickHouse and PostgreSQL is fundamentally a choice between OLAP (Online Analytical Processing) and OLTP (Online Transaction Processing) paradigms. PostgreSQL is a row-oriented relational database perfectly suited for transactional workloads requiring strict ACID compliance, complex multi-table joins, and frequent CRUD (Create, Read, Update, Delete) operations. However, if you attempt to run analytical aggregations over millions of rows, PostgreSQL will become a severe bottleneck.

Conversely, ClickHouse is a fiercely fast, column-oriented database built exclusively for analytics. By storing data by column rather than by row, it can vectorize queries and scan billions of rows in mere milliseconds. I would architect ClickHouse for use cases like large-scale event telemetry, high-volume log storage, or powering real-time analytical dashboards. However, the trade-offs are extreme: ClickHouse has severely limited support for `UPDATE` and `DELETE` operations (relying on asynchronous mutations instead), lacks foreign key constraints, and strongly prefers data ingestion via massive batches rather than single-row inserts (often utilizing a Kafka to ClickHouse pipeline).

---

### Q7: ClickHouse Operations
**Question:** How do you deploy and scale ClickHouse on AWS?

**Expected Answer:**
Deploying and scaling ClickHouse on AWS requires significant operational engineering because AWS does not offer a native managed service for it. For optimal performance, I deploy ClickHouse on EC2 instances featuring locally attached NVMe storage (like the `i3` or `i4i` families) for extreme disk throughput, though `gp3` EBS volumes are acceptable for less aggressive workloads. To achieve high availability, I configure clustering using the `ReplicatedMergeTree` engine, utilizing ClickHouse Keeper (a lightweight, native alternative to ZooKeeper) to coordinate distributed state.

To scale beyond the capacity of a single node, data must be sharded across a `Distributed` table. The selection of the shard key is the most critical architectural decision; a poorly chosen key will result in data hotspots, completely negating the cluster's performance. You scale horizontally by adding shards to increase write throughput, and adding replicas to increase read throughput. Due to the massive operational burden of managing backups, upgrades, and cluster rebalancing manually, I often recommend evaluating ClickHouse Cloud for a fully managed experience, or exploring AWS-native alternatives like Amazon Athena for querying data lakes or Amazon Timestream for strictly time-series data.
