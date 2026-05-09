# Deep Dive: Database Migrations & IaC at Scale

Trilogy manages a portfolio of 50+ products, often requiring consolidation and migration of disparate database engines.

## 1. Heterogeneous Database Migrations
Moving from Oracle/MS-SQL to PostgreSQL/MySQL (AWS Aurora) is a common Trilogy initiative.
*   **AWS SCT (Schema Conversion Tool):** Automating the conversion of stored procedures, triggers, and schemas.
*   **AWS DMS (Database Migration Service):**
    - **CDC (Change Data Capture):** Keeping the target database in sync with the source during the migration window.
    - **LOB Handling:** Strategies for migrating large binary objects (Limited LOB vs. Full LOB mode).
*   **Validation:** Using DMS Data Validation to ensure row counts and column data match exactly.

## 2. Infrastructure as Code (IaC) for a Portfolio
Managing 50+ products requires extreme uniformity.
*   **Standardized Modules:** Creating a "Golden RDS" or "Golden ECS" Terraform module used across all products.
*   **State Isolation:** Using S3 backends with DynamoDB locking, with a separate state file per product to prevent a "blast radius" effect.
*   **CI/CD for IaC:** Using GitHub Actions to run `terraform plan` and `checkov` (security scanning) before any production apply.

## 3. Python for High-Speed Administration
A "Production-grade" Python script for Trilogy must handle:
*   **Concurrency:** Using `threading` or `asyncio` to query 50+ AWS accounts simultaneously.
*   **Error Handling:** Retrying on `RequestLimitExceeded` and logging detailed context for failures.
*   **Output:** Generating clear, actionable reports (JSON/Markdown) that can be fed into AI tools or dashboards.

## 4. Architecting the "10% Rollout"
When applying a risky infrastructure change:
1.  **Dev/Staged:** Validate in isolated environments.
2.  **10% Canary:** Route 10% of traffic to the new infrastructure (using Route 53 Weighted Records or ALB Target Group weights).
3.  **Soak Period:** Monitor error rates and latency for 1-4 hours.
4.  **Full Rollout:** Expand to 100% if monitors remain green.
5.  **Rollback:** The "Big Red Button" script that can revert changes in < 5 minutes.

## Interview Questions
1.  "You need to migrate a 5TB MS-SQL database to AWS Aurora PostgreSQL with < 1 hour of downtime. Walk me through your plan."
2.  "How do you manage Terraform state for 50 different products without it becoming a maintenance nightmare?"
3.  "Describe a time an infrastructure change caused a production incident. How did you roll it back and what did you learn?"
4.  "What are the most common bottlenecks you find when scaling a containerized application to hundreds of instances?"
