# 05: Governance, Security, and Compliance

The JD emphasizes Microsoft Purview and GDPR-compliant frameworks.

## 1. Microsoft Purview Integration
*   **Data Lineage**: Automatically capturing how data flows from source to Silver to Gold and into Power BI reports.
*   **Data Catalog**: Searchable inventory of all data assets in Fabric.
*   **Sensitivity Labels**: Using Microsoft Information Protection (MIP) labels to mark data as "Highly Confidential," which follows the data even when exported.

## 2. Security Patterns
*   **RBAC (Role-Based Access Control)**: Managing permissions at the Workspace level (Admin, Member, Contributor, Viewer).
*   **RLS (Row-Level Security)**: Restricting data access based on user attributes (e.g., a Sales Manager only sees data for their region).
*   **CLS (Column-Level Security)**: Hiding sensitive columns (e.g., SSN, Credit Card info) from unauthorized users.

## 3. GDPR & Compliance
*   **Data Classification**: Identifying PII (Personally Identifiable Information).
*   **Data Masking**: Obfuscating sensitive data in the Silver layer while keeping the original in Bronze (with restricted access).
*   **Data Retention**: Implementing policies to delete or archive data after a certain period.

## Interview Questions
1.  **"How do you implement Row-Level Security in a Fabric Lakehouse?"**
    *   *Ans*: RLS is typically implemented in the SQL Analytics Endpoint of the Lakehouse using standard T-SQL `CREATE SECURITY POLICY` and `FILTER PREDICATE`.
2.  **"What is the difference between Lineage in Purview vs. Lineage in the Fabric Workspace?"**
    *   *Ans*: Fabric Workspace lineage is local to the workspace items. Purview provides cross-workspace and cross-platform lineage (e.g., from an on-prem SQL Server into Fabric).
3.  **"How would you ensure PII data is masked for most users but visible to HR?"**
    *   *Ans*: I would use CLS or a View in the SQL engine that uses a `CASE` statement checking the user's group/role to either show the plain text or a masked string.

## Practice Task
*   Draw a diagram showing how a sensitivity label applied at the source (ADLS Gen2) propagates through the Medallion architecture into Power BI.
