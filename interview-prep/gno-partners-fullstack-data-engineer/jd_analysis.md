# Job Description Analysis: Full-Stack Engineer (Data/AI) - GNO Partners

## Role Overview
**Company:** GNO Partners
**Position:** Full-Stack Engineer (Data/AI Focus)
**Level:** Mid-Level (3-5 years)
**Core Mission:** Replacing manual Amazon report uploads with automated pipelines (SP-API/Ads API) and powering an AI insights layer for Amazon sellers.

## Core Competencies

### 1. Data Engineering & Pipelines
- **Amazon APIs:** Integrating with **SP-API** and **Ads API**.
- **Resilience:** Handling rate limits, retries, pagination, and backfills at scale.
- **Warehouse:** Designing ingestion and aggregation layers in **Postgres/Supabase**.

### 2. Full-Stack Development
- **Backend:** TypeScript, Node.js, **NestJS**.
- **Frontend:** React for building reporting tools and dashboards.
- **Product-Aware:** Understanding how data serves user-facing features.

### 3. Database & Cloud Architecture
- **Postgres Optimization:** Partitioning, indexing, and handling large multi-tenant datasets.
- **AWS Stack:** S3, Lambda, SNS, SQS, EC2.
- **Orchestration:** Using Step Functions or EventBridge for pipeline workflows.

### 4. AI & Insights
- **Structuring Data:** Preparing clean data for LLM-powered analysis.
- **Agentic Basics:** Familiarity with LLM tool-use and agent frameworks.

## Key Interview Themes to Prepare

1.  **Scaling Ingestion:** How do you move from manual file uploads to a multi-tenant, automated API sync without hitting rate limits?
2.  **Handling API Failure:** Designing retry logic and backoff strategies for flaky vendor APIs.
3.  **Postgres Performance:** Discussing partitioning and indexing strategies for a warehouse handling millions of Amazon sales records.
4.  **AWS Event-Driven Pipelines:** Using SQS/SNS and Lambda to build a decoupled ingestion architecture.
5.  **Full-Stack Report Building:** Walk through building a "Profitability Report" from the API layer to the React UI.
6.  **Preparing Data for AI:** How do you structure Amazon report data so an LLM can provide meaningful "Actionable Insights"?

## Technical "Must-Haves" for the Interview
- 3+ years of **TypeScript/Node.js** experience.
- Strong **Postgres/Supabase** optimization skills.
- Proven experience with **AWS Lambda/SQS**.
- Experience with **3rd-party API integration** at scale.
