# Workflow Optimization Best Practices

MKTH is looking for "talented Automation Engineers" who can optimize workflows. This guide covers the engineering mindset required for professional automation.

---

## 1. The "Source of Truth" Principle
*   **Problem:** Syncing data between CRM, Project Management (Asana), and Finance (QuickBooks) often leads to data duplication or conflicts.
*   **Optimization:** Identify one system as the "Source of Truth" for each data type (e.g., Salesforce for Customer info). All other systems must be updated *from* this source, never the other way around, to prevent "Sync Loops."

## 2. Reducing API Latency
*   **Batching:** Instead of triggering a workflow for every single row in a CSV, use an "Aggregator" to bundle them into a single API call (if the destination API supports it).
*   **Parallelism:** Running multiple branches of a workflow simultaneously if the steps don't depend on each other.

## 3. Data Transformation & Cleanup
*   **Normalization:** Ensuring that dates are always in ISO-8601 and currency values are always in a consistent format before they hit the destination system.
*   **Sanitization:** Removing HTML tags or special characters from raw data inputs that might break downstream systems (e.g., a SQL injection risk in a custom database integration).

## 4. Scalability & Maintenance
*   **Modularization:** Instead of one giant 50-step workflow, break it into 5 smaller, specialized sub-workflows.
    *   *Benefit:* Easier to debug, easier to reuse a sub-workflow in a different project, and less risk of the entire system failing if one step breaks.
*   **Monitoring:** Setting up a "Heartbeat" automation that runs every hour to check if the main APIs are still responding.

---

## 5. Case Study: E-commerce Order Fulfillment
*   **Current State:** Staff manually copy data from Shopify to a Google Sheet, then to the Shipping provider.
*   **Optimized Automation:**
    1.  **Trigger:** Shopify Webhook "Order Created."
    2.  **Filter:** Only process "Paid" orders.
    3.  **Action 1:** Update Inventory in Google Sheet.
    4.  **Action 2:** Create Shipping Label and get Tracking ID.
    5.  **Action 3:** Update Shopify with Tracking ID and notify customer.
    6.  **Action 4:** Log transaction in QuickBooks.
*   **Business Impact:** Reduces manual labor by 95%, eliminates human error in shipping addresses, and improves customer satisfaction with real-time updates.
