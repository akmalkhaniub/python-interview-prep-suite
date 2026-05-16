# Portfolio Mapping: Data Architect (Polani GT)

Mapping your **AgenticApps** and engineering experience to the "Source-to-Insight" ownership required by Polani GT.

---

## 1. End-to-End Pipeline Ownership
**Requirement:** Own the data pipeline from source to insight; act as an infrastructure engineer and data analyst.

*   **Project Alignment: [App 2: Revenue Recovery Auditor](file:///g:/ReplitProjects/AgenticApps/revenue-recovery-auditor)**
    *   **The Talking Point:** "In App 2, I didn't just build a bot; I built the data foundation. I ingested raw event data from Stripe, joined it with internal user activity in PostgreSQL, and transformed it into a 'Curated' layer that allowed the agent to make high-stakes financial decisions. This required auditing the raw data for consistency before the logic could even run."
    *   **Key Detail:** Discuss the **Business Translation**—explaining to stakeholders how payment failure patterns correlate with user churn.

---

## 2. BigQuery & Medallion Architecture
**Requirement:** Hands-on experience with BigQuery, medallion architecture, and multi-layer data models.

*   **Project Alignment: [App 9: Fintech Fraud Mitigator](file:///g:/ReplitProjects/AgenticApps/fintech-fraud-mitigator)**
    *   **The Talking Point:** "I designed a multi-layer ingestion and processing system. In a BigQuery context, this maps directly to the **Medallion Architecture**:
        *   **Bronze (Raw):** Landing raw NATS event streams.
        *   **Silver (Curated):** Cleansed and normalized transaction data, partitioned by date for efficient scanning.
        *   **Gold (Reporting):** Aggregated risk profiles used for real-time fraud dashboards."
    *   **Key Detail:** Mention **Partitioning & Clustering**—how you ensure that high-volume transaction data (like travel bookings) is scanned efficiently.

---

## 3. Custom Ingestion & Cloud Functions
**Requirement:** Experience with Airbyte, Cloud Functions, or custom pipeline development.

*   **Project Alignment: [App 10: Model Router / Background Jobs](file:///g:/ReplitProjects/AgenticApps/model-router-sentinel)**
    *   **The Talking Point:** "I've built custom ingestion logic using **Node.js/Python** that acts similarly to Cloud Functions. For Polani GT, I would leverage event-driven functions to trigger ingestion as soon as a 3rd party operational file (like a GDS report) lands in Cloud Storage, ensuring the 'Freshness' of the data warehouse."
    *   **Key Detail:** Discussing **Error Handling** and **Retries** in pipelines—ensuring that a single API failure doesn't break the entire daily batch.

---

## 4. Data Governance & PII Controls
**Requirement:** Implement governance controls and appropriately secured warehouse layers.

*   **Project Alignment: [App 16: Compliance PII Sanitizer](file:///g:/ReplitProjects/AgenticApps/compliance-pii-sanitizer)**
    *   **The Talking Point:** "Governance is critical when handling travel/transaction data. I built a PII sanitizer that ensures sensitive user data is masked before it hits the reporting layer. At Polani GT, I would implement **Column-level security** and **Authorized Views** in BigQuery to ensure that analysts only see the data they need, while infrastructure engineers maintain the raw truth."
