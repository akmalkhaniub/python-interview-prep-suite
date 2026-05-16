# JD Analysis: Data Engineer — Architect (Polani GT)
## Company: Polani GT | Location: Karachi | Role: Hybrid (Infra + Analyst + Translator)

### 1. The "Pivotal" Hybrid Requirement
This role is NOT just about writing SQL. It's about **ownership**. You are expected to:
- **Audit:** Look at the mess and propose the "Clean" version.
- **Architect:** Decide *how* data flows (not just fixing broken pipes).
- **Translate:** Tell the Head of Data & AI *why* a certain use case is or isn't feasible based on real data constraints.

### 2. Core Technical Pillars
| Pillar | Key Requirements | Related Portfolio Projects |
| :--- | :--- | :--- |
| **Data Architecture** | Medallion Architecture (Raw, Curated, Reporting layers), Schema design. | **App 9 (Fraud Detection)**: Multi-layer event processing. **App 2 (Revenue Auditor)**: Data normalization across Stripe/PostgreSQL. |
| **BigQuery Mastery** | Partitioning, Clustering, Cost Governance, SQL optimization. | *General GCP experience*: Optimizing analytical queries for high-volume datasets. |
| **Ingestion Strategy** | Airbyte, Cloud Functions, Custom pipelines for 3rd party operational data. | **App 10 (Background Jobs)**: Custom job processing. **App 5 (Support Agent)**: Real-time data sync. |
| **Analytics Engineering** | dbt (nice to have), Multi-layer data modeling, BI layer (Looker/Power BI). | **App 3 (Scientific Sandbox)**: Structured data extraction and analysis. |
| **Governance & Quality** | Data quality checks, partitioning, clustering, access controls. | **App 16 (Compliance Sanitizer)**: PII controls and data governance. |

### 3. Industry Context: Travel & E-commerce
Polani GT is a major travel group in Pakistan. Their data is likely:
- **Transaction-intensive:** Bookings, refunds, cancellations (high volume).
- **Third-party heavy:** GDS data (Amadeus/Sabre), Airline APIs, Hotel feeds (messy, unstructured).
- **Time-sensitive:** Dynamic pricing and inventory management.

---
> [!TIP]
> **Key Strategy for Polani GT:** Focus on **Cost-Performance Balancing**. In BigQuery, anyone can run a query, but a "Architect" runs it efficiently using partitioning and clustering to save the company money. Mention your ability to **Audit** existing debt.
