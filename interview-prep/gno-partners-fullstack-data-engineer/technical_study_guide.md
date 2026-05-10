# Technical Study Guide: Full-Stack Engineer (GNO Partners)

## 1. Amazon SP-API & Ads API
- **Authentication:** Selling Partner API (LWA - Login with Amazon) and OAuth.
- **Rate Limiting:** Understanding the "Token Bucket" algorithm used by Amazon and implementing client-side rate limiting.
- **Reporting:** Requesting, polling, and downloading Amazon reports (JSON/CSV).
- **Ads API:** Managing campaigns, ad groups, and keyword performance data.

## 2. Postgres & Supabase Optimization
- **Table Partitioning:** Using `LIST` or `RANGE` partitioning for multi-tenant data (e.g., partitioning by `seller_id`).
- **Indexing:** B-Tree, GIN (for JSONB), and partial indexes to speed up report queries.
- **Query Optimization:** Analyzing `EXPLAIN ANALYZE` output to find slow joins or sequential scans.
- **JSONB:** Best practices for storing flexible Amazon report schemas.

## 3. AWS Serverless Pipelines
- **Lambda:** Processing ingestion tasks and triggered by SQS/S3 events.
- **SQS/SNS:** Decoupling the "API Polling" logic from the "Data Processing" logic.
- **S3:** Staging raw API responses before transformation into Postgres.
- **Step Functions:** Orchestrating complex multi-step syncs (e.g., Auth -> Request Report -> Poll -> Download -> Process).

## 4. Full-Stack: NestJS & React
- **NestJS:** Using DTOs, Controllers, and Services for a clean backend architecture.
- **React:** Building performant data tables and charts (e.g., using TanStack Table or Recharts).
- **State Management:** Efficiently handling large report datasets in the browser.

## 5. AI Insights & Data Quality
- **Data Structuring:** Transforming raw Amazon records into concise summaries suitable for LLM prompts.
- **Tool Use:** How an AI Agent might call a "Get Inventory Levels" tool to provide advice.
- **Observability:** Monitoring pipeline health (e.g., error rates, latency) using CloudWatch.

## 6. System Design
- **Idempotency:** Ensuring that re-running a pipeline task doesn't create duplicate sales records.
- **Incremental Sync:** Using `updated_at` timestamps or cursors to only fetch new data.

## Resources to Review
- [Amazon SP-API Documentation](https://developer-docs.amazon.com/sp-api/)
- [PostgreSQL Table Partitioning Guide](https://www.postgresql.org/docs/current/ddl-partitioning.html)
- [AWS Serverless Data Pipeline Patterns](https://serverlessland.com/patterns)
- [NestJS: Working with Databases](https://docs.nestjs.com/techniques/database)
- [TanStack Table (React Table) Documentation](https://tanstack.com/table/v8)
