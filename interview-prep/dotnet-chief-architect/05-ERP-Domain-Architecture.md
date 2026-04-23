# ERP & Domain Architecture

**Focus:** ERP product development, application architecture, multi-tenant design

---

### Q1: ERP Architecture Patterns
**Question:** What are the unique architectural challenges of building an ERP system compared to a typical SaaS product?

**Expected Answer:**
- **Domain complexity**: Dozens of interconnected modules (HR, Finance, Procurement, Assets) with deep business rules.
- **Data integrity**: Financial transactions must be ACID. Double-entry bookkeeping. Audit trails for every change.
- **Configurability**: Every organization has unique workflows. Must support customization without code changes (configurable rules, custom fields, workflow engines).
- **Regulatory compliance**: Tax laws, labor regulations, reporting standards (GAAP, IFRS) vary by jurisdiction.
- **Backward compatibility**: Customers on older versions. Schema migrations can't break existing functionality.
- **Performance at scale**: GL posting runs, payroll calculations, report generation — batch processing with 100K+ records.
- **Integration**: Every ERP integrates with banks, tax authorities, HR systems, procurement portals.

---

### Q2: Multi-Tenancy
**Question:** How do you design multi-tenancy for an ERP serving nonprofits, school boards, and government agencies?

**Expected Answer:**
- **Database-per-tenant**: Strongest isolation. Easier compliance (data residency). Higher operational cost.
- **Schema-per-tenant**: Shared database, separate schemas. Good balance of isolation and cost.
- **Shared schema with tenant ID**: Cheapest. Row-level security. Risk: data leakage bugs.
- **ERP recommendation**: **Database-per-tenant** for government (regulatory) and **schema-per-tenant** for others.
- **EF Core**: Global query filters `builder.HasQueryFilter(e => e.TenantId == currentTenant)`.
- **Connection management**: Resolve connection string per tenant from a central registry at request start.
- **Migrations**: Must run against ALL tenant databases. Use a migration orchestrator.

---

### Q3: Workflow Engine
**Question:** The ERP needs configurable approval workflows (PO approvals, leave requests, budget changes). How do you architect this?

**Expected Answer:**
- **State machine pattern**: Each workflow is a finite state machine (Draft → Submitted → Approved/Rejected).
- **Engine options**: Elsa Workflows (.NET-native), or custom state machine with `Stateless` library.
- **Configuration**: Business users define rules in admin UI — "POs over $10K require Director approval."
- **Persistence**: Store workflow state in DB. Resume after crashes. Compensating actions for rollbacks.
- **Notifications**: Each state transition triggers notifications (email, in-app) via domain events.
- **Audit**: Log every state transition with timestamp, actor, comments.
- **Testing**: Workflow definitions are data — test all paths with parameterized unit tests.

---

### Q4: Reporting Architecture
**Question:** Users need real-time dashboards AND heavy batch reports (year-end financials). How do you design the reporting layer?

**Expected Answer:**
- **Real-time dashboards**: Read-optimized views or materialized views on the operational DB. Refreshed on write.
- **Heavy reports**: Separate read replica or data warehouse. Don't run year-end reports on the production OLTP database.
- **CQRS benefit**: Read models pre-aggregated for common queries (budget summary, headcount by department).
- **Report generation**: Background jobs (Hangfire/Quartz) for PDF/Excel generation. Queue-based to handle spikes.
- **Technology**: SQL Server Reporting Services (SSRS) for legacy, or modern: custom report builder with Blazor + export to Excel/PDF via libraries (ClosedXML, QuestPDF).
- **Caching**: Cache expensive aggregations. Invalidate on write. Redis for shared cache across instances.

---

### Q5: Data Migration & Upgrades
**Question:** Customers are upgrading from v5 to v8 of the ERP. Three major schema versions in between. How do you handle this?

**Expected Answer:**
- **Sequential migrations**: v5 → v6 → v7 → v8. Each version has idempotent migration scripts.
- **Tool**: EF Core Migrations or DbUp/Flyway for raw SQL scripts.
- **Data transformations**: Some migrations are schema-only, others transform data (split a column, merge tables).
- **Testing**: Run migration against a copy of production data in staging. Compare row counts, checksums.
- **Rollback**: Every migration has a down script. But in practice, take a full backup before migrating.
- **Zero-downtime**: Use expand-contract pattern. Add new columns → deploy code that writes to both → backfill → drop old.
- **Communication**: Maintenance window for large migrations. Notify customers 2 weeks in advance.
