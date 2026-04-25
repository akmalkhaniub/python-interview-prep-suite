# Healthcare Compliance & Correctness
**Focus:** Financial Correctness, CQC/HIPAA Compliance, Audit Logging, RCA

---

## Section A: Correctness & Mission-Critical Systems

### Q1: Financial & Mathematical Correctness
**Question:** How do you ensure absolute mathematical correctness when processing payroll, invoicing, or mileage tracking in software?

**Expected Answer:**
Handling financial data requires uncompromising exactness; simple floating-point arithmetic errors can result in thousands of pounds of payroll discrepancies over time. The primary rule is: **Never use standard floating-point numbers (floats/doubles) to represent currency.** Because of how binaries represent decimals, operations like `0.1 + 0.2` yield `0.30000000000000004` in JavaScript.

To guarantee correctness in Node.js and PostgreSQL, I strictly store monetary values as integers representing the smallest currency unit (e.g., pence or cents: £10.50 is stored as `1050`). Alternatively, I use the precise `DECIMAL(10,2)` datatype in PostgreSQL, coupled with arbitrary-precision decimal libraries like `decimal.js` or `big.js` in the Node backend to perform all mathematical additions, tax calculations, and rounding. Furthermore, I implement robust double-entry bookkeeping ledgers within the database architecture, ensuring that every financial transaction has a corresponding credit and debit that strictly sum to zero, providing absolute mathematical auditability.

### Q2: Compliance in Regulated Domains (CQC / HIPAA)
**Question:** Describe the technical implications of building software in a heavily regulated domain governed by bodies like the CQC (Care Quality Commission) or HIPAA.

**Expected Answer:**
Building for regulated healthcare domains shifts security and compliance from a "best practice" to a strict legal requirement. The fundamental implication is the mandate for **Data Privacy and Encryption**. All Protected Health Information (PHI) must be encrypted both "in transit" (enforcing TLS 1.2+ across all API and Load Balancer endpoints) and "at rest" (utilizing AWS KMS to encrypt the underlying EBS volumes of the RDS databases and S3 buckets).

Furthermore, compliance dictates strict access controls. It is legally unacceptable for a developer to manually execute SQL queries against the production patient database to troubleshoot a bug. We must implement comprehensive Role-Based Access Control (RBAC) at the application layer, ensuring care workers can only access records relevant to their assigned shifts. Finally, regulated platforms must prioritize absolute **Data Durability**; a lost care plan could have catastrophic real-world patient safety consequences, necessitating aggressive database Point-in-Time Recovery (PITR) policies and routine, validated disaster recovery drills.

---

## Section B: Auditing & Incident Management

### Q3: Implementing Robust Audit Logging
**Question:** How do you implement robust audit logging to track who viewed or modified sensitive patient data?

**Expected Answer:**
In compliance-heavy domains, an application log stating "Rota Updated" is insufficient. Auditors demand a complete chain of custody answering: *Who* made the change, *When*, *What* exact fields were altered, and *Why*.

To implement this, I avoid sprinkling manual logging statements throughout the business logic. Instead, I utilize the **Event Sourcing** pattern or database-level mechanisms. In PostgreSQL, I would employ an Audit Trigger function that fires automatically on any `INSERT`, `UPDATE`, or `DELETE` to sensitive tables. This trigger captures the old JSON payload, the new JSON payload, and the user ID (passed down via session context), writing an immutable record to a dedicated, append-only `Audit_Logs` table. Because it is handled at the database level, no bug in the Node.js application can accidentally bypass the auditing requirement, guaranteeing absolute compliance integrity.

### Q4: Root Cause Analysis (RCA) in Mission-Critical Systems
**Question:** What is your process for Root Cause Analysis (RCA) when a critical bug affects a regulated, mission-critical domain like payroll or patient safety?

**Expected Answer:**
When an incident occurs in a mission-critical domain, the immediate priority is always rapid mitigation and containment. Once the bleeding is stopped and the system is stable, the RCA phase begins. I conduct a blameless post-mortem, operating under the assumption that the engineers acted correctly based on the system's design, and the system itself failed to protect them.

I employ the **"5 Whys"** methodology to drill past the surface symptom. If payroll failed (Why? Because the worker's timesheet returned `null`. Why? Because the API timed out. Why? Because a database index was missing). The resulting RCA document must detail the specific timeline, the customer impact, the root cause, and most importantly, highly specific, actionable Jira tickets to prevent recurrence. If an incident affects PHI or financial accuracy, the RCA must be escalated transparently to compliance and leadership teams to evaluate if external regulatory reporting is mandated.

### Q5: Balancing Speed and Compliance
**Question:** The job description states features are "piloted the same week they ship." How do you balance the need for rapid feature iteration with the strict compliance requirements of healthcare?

**Expected Answer:**
Agility and compliance are often viewed as opposing forces, but they can coexist through aggressive automation and architectural decoupling. You cannot wait for a two-week manual security audit before every deployment. Instead, I "shift-left" compliance checks directly into the CI/CD pipeline. Automated static analysis (SAST) tools scan every Pull Request for exposed secrets or SQL injection vulnerabilities, and container scanning ensures base images comply with security protocols instantly.

Architecturally, I balance speed by utilizing **Feature Flags**. I can merge and deploy new AI scheduling features to production rapidly, hidden entirely behind a feature toggle. This allows us to expose the volatile new feature exclusively to a single, trusted pilot care agency for beta testing in the real world. If the pilot uncovers a critical compliance or functional issue, I can instantly disable the feature flag to roll back the change without requiring a high-stress, coordinated deployment rollback, ensuring the wider platform remains perfectly stable and compliant.
