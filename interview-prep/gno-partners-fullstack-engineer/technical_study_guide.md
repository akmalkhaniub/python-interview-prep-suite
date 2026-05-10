# Technical Study Guide: Full-Stack Engineer (GNO Partners)

## 1. NestJS & Backend Architecture
- **Dependency Injection:** Understanding how to structure services, controllers, and modules.
- **DTOs & Validation:** Using `class-validator` to ensure clean ingestion of Amazon data.
- **Microservices Pattern:** How NestJS can handle background tasks and API requests in a decoupled way.

## 2. Postgres Data Modeling (Multi-Tenant)
- **Schema Design:** Modeling Sellers, Products, Orders, and Ad Campaigns.
- **Query Optimization:** Using indexes and avoiding N+1 problems in report generation.
- **Supabase Integration:** Leveraging Row-Level Security (RLS) for data isolation.

## 3. CSV/Excel Processing at Scale
- **Streaming:** Using `csv-parse` or similar to process large files without exhausting memory.
- **Normalization:** Mapping inconsistent column headers from manual Amazon reports into a unified schema.
- **Bulk Operations:** Generating valid CSV files that sellers can upload back to Amazon.

## 4. AWS Plumbing for Analytics
- **S3:** Managing "Upload" buckets and "Processed" buckets for report history.
- **Lambda:** Triggering transformations when a file lands in S3.
- **SNS/SQS:** Managing the queue of report generation tasks.

## 5. React for Data Dashboards
- **State Management:** Handling complex table states (sorting, filtering, pagination).
- **Data Visualization:** Using libraries like Recharts or D3 to show sales trends.
- **Performance:** Memoization and efficient re-rendering for large data grids.

## 6. Basic Agentic AI
- **LLM Tool-Use:** How to define a "Get Sales Data" tool for an agent.
- **Prompt Engineering:** Writing prompts that summarize complex Amazon metrics into 1-2 "Actionable Insights."
- **LangChain:** Basics of chains and simple agent loops.

## Resources to Review
- [NestJS: Documenting your API with Swagger](https://docs.nestjs.com/openapi/introduction)
- [PostgreSQL: Indexes and Performance](https://www.postgresql.org/docs/current/indexes.html)
- [AWS Lambda: S3 Trigger Tutorial](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html)
- [React: Optimizing Large Lists/Tables](https://react.dev/learn/render-and-commit)
- [Amazon SP-API: Reports API Overview](https://developer-docs.amazon.com/sp-api/docs/reports-api-v2021-06-30-use-case-guide)
