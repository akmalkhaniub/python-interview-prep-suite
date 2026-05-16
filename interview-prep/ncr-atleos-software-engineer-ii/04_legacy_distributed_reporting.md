# Distributed Architecture, Reporting & Hadoop

NCR Atleos has a specialized requirement for Hadoop and enterprise reporting tools. This guide prepares you for these "Big Data" and "Insight" conversations.

---

## 1. Distributed Architecture with Hadoop
*   **The HDFS (Hadoop Distributed File System):** Understanding how data is distributed across a cluster of commodity hardware. 
*   **MapReduce:** The "Divide and Conquer" approach to processing massive datasets.
*   **Why at NCR?** Likely used for analyzing **billions of ATM transactions** to identify fraud patterns, maintenance needs, or cash-level trends.
*   **Talking Point:** "In distributed systems, I focus on **Data Locality**. Moving the computation to the data (Hadoop's core principle) is far more efficient than moving petabytes of data to the compute engine."

## 2. Enterprise Reporting: Crystal Reports
*   **What it is:** A pixel-perfect reporting tool used for complex financial statements, receipts, and audit logs.
*   **Integration:** Often embedded in .NET WinForms or WebForms applications.
*   **The Challenge:** Designing sub-reports and complex groupings that don't timeout the web server.
*   **Talking Point:** "I have experience designing structured reports that translate complex SQL datasets into human-readable, printable financial documents, ensuring that every decimal point is accurate for audit compliance."

## 3. Modern Insights: Power BI
*   **The Workflow:** Data Ingestion (SQL/Hadoop) -> Data Transformation (Power Query/DAX) -> Visualization.
*   **Live Connection vs. Import:** Deciding when to use DirectQuery (for real-time ATM status) versus Import (for historical trend analysis).
*   **Talking Point:** "I transition enterprises from static Crystal Reports to interactive Power BI dashboards, allowing regional managers to drill down into their branch performance in real-time."

## 4. Continuous Improvement & Root Cause Analysis
*   **The 5 Whys:** A technique for identifying the root of a problem (e.g., "The server crashed" -> "Why?" -> "Out of memory" -> "Why?" -> "Memory leak in C++ subsystem" -> etc.).
*   **Peer Review:** Not just for finding bugs, but for **Knowledge Transfer**. Using code reviews to spread best practices across the team.
*   **Talking Point:** "I don't just fix the immediate defect. I look for the **systemic failure**—was it a lack of testing, a gap in requirements, or a performance bottleneck in the architecture? This is how I ensure 'Software Engineering Best Practices' are lived, not just documented."
