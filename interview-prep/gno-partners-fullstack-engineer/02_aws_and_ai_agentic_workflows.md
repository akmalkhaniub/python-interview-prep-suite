# Deep Dive: AWS Plumbing & Agentic AI Workflows

GNO Partners is layering AI-driven insights across their platform and scaling their AWS infrastructure.

## 1. AWS Plumbing for Seller Tools
*   **The S3/Lambda Trigger:**
    - User uploads a file -> S3 `ObjectCreated` event -> Lambda `processor` function.
    - Lambda validates the file and pushes a message to SQS for the "Aggregator" service to pick up.
*   **EC2 for Heavy Lifting:** While Lambda is great for quick transformations, an EC2 instance might be needed for long-running, multi-step report generation tasks that exceed the 15-minute Lambda limit.

## 2. Basic Agentic AI for Insights
*   **Defining "Insights":**
    - "Your SKU-XYZ has a high ACOS but low conversion. Suggest reducing bid or improving images."
*   **The Orchestration:**
    - Using an LLM (like Claude) with access to a "Snapshot" of the seller's data.
    - **Tool Use:** The agent might call a `get_competitor_pricing` tool to provide better advice.
*   **Pragmatic Approach:** You don't need a PhD in AI; you need to understand how to write robust prompts and manage the LLM's context window.

## 3. Designing for Async Excellence
*   **Communication:** GNO Partners works async. This means your code needs to be well-documented, and your PRs should include the "Why" and "How" clearly.
*   **Infrastructure as Code (IaC):** Using CDK or Terraform to ensure the "AWS Plumbing" is reproducible across Dev/Stage/Prod environments.

## 4. Multi-Tenant Data Modeling
*   **Isolation:** Ensuring that "Seller A's" data is never leaked to "Seller B."
*   **Scalability:** Partitioning the `orders` table by `seller_id` or `month` to keep queries fast as the platform grows.

## Interview Questions
1.  "How would you use AWS SQS and Lambda to handle a burst of 1,000 concurrent report uploads?"
2.  "What is your approach to prompt engineering for a feature that needs to summarize financial data accurately?"
3.  "Describe a scenario where you would use a 'Multi-Agent' approach for an analytics task instead of a single LLM call."
4.  "How do you ensure your AWS infrastructure is secure and cost-efficient for a growing B2B SaaS startup?"
