# Deep Dive: Full-Stack Report Building & Data Aggregation

At GNO Partners, you build the tools that turn raw Amazon data into profit-driving optimizations.

## 1. End-to-End Feature Architecture
*   **The Ingestion Layer:**
    - **Now:** Handling manual `.csv` and `.xlsx` uploads via S3 and Lambda.
    - **Future:** Polling Amazon SP-API for automated report generation.
*   **The Aggregation Engine:**
    - Merging Advertising Spend (Ads API) with Sales Revenue (SP-API) to calculate **ROAS (Return on Ad Spend)** and **ACOS**.
    - Handling "Currency Conversions" for global sellers.
*   **The Presentation Layer:**
    - A React-based "Analytics Dashboard" that lets sellers filter by SKU, Date, or Campaign.

## 2. Processing Large Datasets in Node.js
*   **Memory Management:** Avoiding `fs.readFileSync` for large files. Instead, use `stream` and `pipe` to process records one by one.
*   **Background Jobs:** Using **BullMQ** or simple AWS SQS queues to prevent long-running report generations from blocking the main API thread.

## 3. Designing for "Bulk Operations"
*   **The Workflow:**
    1.  User views a report of underperforming ads.
    2.  User selects 100 ads and clicks "Decrease Bid by 10%."
    3.  The system generates a specific Amazon-formatted `.csv` file.
    4.  User uploads this file back to Amazon Seller Central (or the system pushes it via API).
*   **Precision:** Ensuring that "SKUs" and "Marketplace IDs" are never mixed up during the transformation.

## 4. NestJS Service Patterns
*   `AmazonService`: Handles API/S3 interactions.
*   `ReportService`: Handles the mathematical aggregation and CSV parsing.
*   `BulkOpsService`: Logic for generating the return files for Amazon.

## Interview Questions
1.  "How would you design a React table that remains performant with 5,000 rows of complex Amazon sales data?"
2.  "Describe your strategy for handling inconsistent column names in CSV files uploaded by different clients."
3.  "Walk me through the database schema you would design to track daily sales across multiple Amazon Marketplaces (US, UK, DE)."
4.  "What are the benefits of using NestJS for a data-heavy analytics platform vs. a standard Express app?"
