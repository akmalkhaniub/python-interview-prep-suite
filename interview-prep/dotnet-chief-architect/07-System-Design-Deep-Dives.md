# System Design & Complex Problem Solving

**Focus:** Deep dives, complex solutions, prototypes, ITDs (Integration Test Documents)

---

### Q1: Design a Payroll Processing System
**Question:** Design the payroll module for an ERP serving school boards with 5,000+ employees each. Requirements: multiple pay schedules, tax calculations, deductions, direct deposits.

**Expected Answer:**
- **Domain model**: `PayRun` (aggregate root) → `PaySlip` → `Earnings[]`, `Deductions[]`, `Taxes[]`.
- **Processing pipeline**: Collect attendance → calculate gross → apply deductions → calculate taxes → generate pay slips → export to bank file (ACH/EFT).
- **Batch processing**: Hangfire background job. Process employees in parallel batches of 100.
- **Tax engine**: Pluggable strategy pattern per jurisdiction. Federal → Provincial/State → Local.
- **Idempotency**: Pay run ID ensures no double processing. Status: Draft → Calculated → Approved → Paid.
- **Audit**: Every calculation step logged. Auditors need to see "why was this amount $X?"
- **Performance**: Pre-calculate and cache tax tables. Avoid N+1 queries on employee deductions.
- **Rollback**: Void and reissue, not delete. Financial records are immutable.

---

### Q2: Design a Configurable Report Builder
**Question:** Users want to build custom reports without developer involvement. Design a report builder.

**Expected Answer:**
- **Report definition**: Stored as JSON/YAML — data source, filters, columns, grouping, sorting.
- **Data layer**: Expose safe, pre-defined data views (not raw tables). Users select from "Employee Directory", "GL Transactions", etc.
- **Query builder**: Translate report definition → parameterized SQL. NEVER concatenate user input.
- **Rendering**: Server-side rendering to HTML, PDF (QuestPDF), Excel (ClosedXML).
- **Scheduling**: Recurring reports via background job. Email delivery.
- **Permissions**: Row-level security. A department head sees only their department's data.
- **Caching**: Cache report results for X minutes. Invalidate on underlying data change.
- **Performance guardrails**: Max rows (10,000 for interactive), timeout (30 seconds), query cost estimation.

---

### Q3: Design a Secure Document Management System
**Question:** The ERP needs to store and manage sensitive documents (tax forms, contracts, ID scans). Design the architecture.

**Expected Answer:**
- **Storage**: Azure Blob Storage / AWS S3. Never in the database (except metadata).
- **Encryption**: At-rest (SSE with customer-managed keys), in-transit (HTTPS/TLS).
- **Access control**: Pre-signed URLs with expiration (15 min). No direct bucket access.
- **Virus scanning**: Scan on upload before making available (ClamAV or cloud-native scanning).
- **Metadata**: DB stores file reference, uploader, timestamp, document type, related entity (Employee, PO).
- **Versioning**: Keep all versions. Soft-delete only. Retention policies per document type.
- **Search**: Full-text search via Elasticsearch on extracted text (OCR for scanned documents).
- **Compliance**: GDPR right-to-erasure — must be able to permanently delete a user's documents.

---

### Q4: Performance Deep Dive
**Question:** The GL (General Ledger) posting process takes 45 minutes for a large school board. How do you diagnose and fix this?

**Expected Answer:**
- **Diagnose**: Profiling with dotTrace or Application Insights. Find the hot path.
- **Common causes**:
  - **N+1 queries**: Loading account details one-by-one inside a loop. Fix: batch load with `IN` clause.
  - **Single-threaded**: Process entries sequentially. Fix: `Parallel.ForEachAsync` with partitioning.
  - **Lock contention**: Row-level locks on the GL summary table. Fix: batch updates, reduce transaction scope.
  - **Missing indexes**: Sequential scan on `AccountId + Period`. Add composite index.
  - **Excessive logging**: Logging every entry in a tight loop. Fix: batch log, reduce verbosity.
- **Target**: < 5 minutes. Optimize the biggest bottleneck first (Pareto — 80% of time in 20% of code).
- **Regression prevention**: Add a performance benchmark test. Alert if GL posting exceeds 5 min threshold.

---

### Q5: Integration Test Documents (ITDs)
**Question:** What is an ITD and how do you create and execute one for a new module?

**Expected Answer:**
- **ITD**: A structured document defining integration test scenarios for a feature/module. Maps business requirements to testable scenarios.
- **Structure**:
  - **Preconditions**: System state before test (data setup, configuration).
  - **Steps**: Exact actions to perform (API calls, UI interactions).
  - **Expected results**: Specific assertions (status codes, DB state, UI output).
  - **Data requirements**: Test data needed (tenant, users, seed records).
- **Coverage**: Happy path + error cases + boundary conditions + concurrent access.
- **Execution**: Can be manual (QA team) or automated (converted to integration tests in code).
- **Traceability**: Each ITD maps to requirements/user stories. Ensures nothing is untested.
- **Review**: Architect reviews ITDs before sprint starts. Identifies missing scenarios.

---

### Q6: Concurrency & Optimistic Locking
**Question:** Two users edit the same Purchase Order simultaneously. How do you handle this?

**Expected Answer:**
- **Optimistic concurrency**: EF Core `[ConcurrencyCheck]` or `[Timestamp]` attribute on a `RowVersion` column.
```csharp
public class PurchaseOrder
{
    public int Id { get; set; }
    [Timestamp]
    public byte[] RowVersion { get; set; }
    // ... other properties
}
```
- **On conflict**: EF throws `DbUpdateConcurrencyException`. Catch it and:
  - Show user: "This record was modified by another user. Reload and try again."
  - OR: Merge changes automatically if fields don't overlap.
- **Pessimistic locking**: `SELECT FOR UPDATE`. Use only for critical financial operations (not general CRUD).
- **UI**: Show a "last modified by X at Y" indicator. Disable save button if stale.
