# Data Services: RDS PostgreSQL & ClickHouse

**Focus:** RDS Management, PostgreSQL Ops, ClickHouse, Database DevOps

---

## Section A: RDS PostgreSQL Operations

### Q1: RDS Architecture Decisions
**Question:** Walk through your decisions for a production PostgreSQL deployment: Multi-AZ, Read Replicas, instance sizing, storage.

**Expected Answer:**
- **Multi-AZ**: Always for production. Synchronous standby, automatic failover (60–120s).
- **Read Replicas**: For read-heavy workloads. Async replication. Can be cross-region for DR.
- **Instance**: Start with `db.r6g.large` (Graviton, memory-optimized). Use Performance Insights to right-size.
- **Storage**: `gp3` (baseline 3000 IOPS, scalable). `io2` for extreme IOPS.
- **Encryption**: Enable at creation with CMK. Cannot add later.
- **Parameter Group**: Tune `shared_buffers`, `work_mem`, `max_connections`.

---

### Q2: Backup & Recovery
**Question:** Production RDS is corrupted by a bad migration. How do you recover?

**Expected Answer:**
- **Automated backups**: Daily snapshots + WAL logs. Retention up to 35 days.
- **Point-in-Time Recovery (PITR)**: Restore to any second. Creates a NEW instance.
- **Process**: PITR restore → verify → promote → update connection strings.
- **Manual snapshots**: Take before every migration. No retention limit.
- **RTO/RPO**: PITR gives RPO ~5 minutes. RTO depends on DB size.

---

### Q3: Performance Troubleshooting
**Question:** App latency spikes — you suspect the database. How do you diagnose?

**Expected Answer:**
- **Performance Insights**: Top SQL by wait events, load by waits.
- **Common waits**: `IO:DataFileRead` (need IOPS/memory), `Lock:transactionid` (contention), `CPU` (bad queries).
- **`pg_stat_statements`**: Find slow queries by mean/total time.
- **Connection pooling**: Use PgBouncer or RDS Proxy if seeing `Client:ClientRead` waits.
- **Vacuum**: Check `pg_stat_user_tables` for dead tuples. Missing autovacuum = table bloat.
- **Index analysis**: `EXPLAIN ANALYZE` on slow queries.

---

## Section B: Database DevOps

### Q4: Schema Migrations in CI/CD
**Question:** How do you safely run migrations on production PostgreSQL via GitLab CI?

**Expected Answer:**
- **Tool**: Alembic, Flyway, or Sqitch.
- **Expand-Contract**: (1) Add column (nullable) (2) dual-write (3) backfill (4) switch reads (5) drop old.
- **Non-locking DDL**: `CREATE INDEX CONCURRENTLY`, nullable `ADD COLUMN` instant in PG 11+.
- **Pipeline**: Migration as dedicated CI stage BEFORE app deployment.
- **Connection**: CI runner via VPC. Credentials from Secrets Manager.

---

### Q5: RDS Proxy
**Question:** Why use RDS Proxy in front of PostgreSQL?

**Expected Answer:**
- **Problem**: PostgreSQL process-per-connection. 1000 Lambda invocations = 1000 DB connections.
- **RDS Proxy**: Connection pooling + multiplexing. Warm pool, reuses connections.
- **Failover**: Faster failover (~20s vs ~60s) during Multi-AZ switch.
- **IAM Auth**: No passwords in code.
- **Alternative**: Self-managed PgBouncer (more control, more ops).

---

## Section C: ClickHouse

### Q6: ClickHouse vs. PostgreSQL
**Question:** When would you choose ClickHouse over PostgreSQL?

**Expected Answer:**
- **ClickHouse**: Column-oriented OLAP. Analytical queries over billions of rows.
- **PostgreSQL**: Row-oriented OLTP. Transactional workloads (CRUD, joins, constraints).
- **Use ClickHouse for**: Event analytics, log storage, time-series, real-time dashboards.
- **Performance**: ClickHouse scans 1B rows in seconds.
- **Trade-offs**: Limited UPDATE/DELETE, no foreign keys, eventual consistency in distributed mode.
- **Ingestion**: Batch inserts preferred. Kafka → ClickHouse is common.

---

### Q7: ClickHouse Operations
**Question:** How do you deploy and scale ClickHouse on AWS?

**Expected Answer:**
- **Deployment**: EC2 with local NVMe (i3/i4i) or EBS gp3.
- **Clustering**: ReplicatedMergeTree + ClickHouse Keeper for coordination.
- **Sharding**: Distributed table. Shard key choice is critical (avoid hotspots).
- **Scaling**: Add shards for writes, replicas for reads.
- **Challenges**: No managed AWS service. Must manage upgrades, backups, monitoring.
- **Alternatives**: ClickHouse Cloud (managed), Athena (S3 queries), Timestream (time-series).
