# Deep Dive: AWS Serverless & Event-Driven Architecture

Ciklum's client operates in the sustainability tech space, requiring scalable and cost-effective solutions using AWS Serverless.

## 1. Designing with AWS Lambda
*   **Triggers:** S3 uploads, SNS messages, SQS queues, or API Gateway requests.
*   **Execution Model:** Stateless, short-lived. How to handle database connections (e.g., using RDS Proxy or DynamoDB)?
*   **Performance:** Memory allocation affects CPU power. Use AWS Lambda Power Tuning to find the sweet spot.

## 2. NoSQL Modeling with DynamoDB
*   **Single-Table Design:** Instead of multiple tables, use GSI and Sort Keys to store different entity types in one table for single-request retrieval.
*   **Consistency:** Eventual consistency vs. Strong consistency.
*   **Streams:** Using DynamoDB Streams to trigger Lambdas for real-time data processing (e.g., updating sustainability metrics).

## 3. Asynchronous Messaging (SNS/SQS)
*   **Fan-out Pattern:** One SNS topic pushing to multiple SQS queues (e.g., a "Waste Recorded" event triggering both an "Analytics" service and an "Alert" service).
*   **FIFO Queues:** Ensuring messages are processed in the exact order they were sent.
*   **Backoff & Retries:** Configuring visibility timeouts and redrive policies to handle partial failures gracefully.

## 4. API Gateway & Security
*   **Authorizers:** Lambda Authorizers for custom JWT validation.
*   **CORS:** Configuring cross-origin resource sharing for React/RN frontends.
*   **Throttling:** Protecting backend resources from spikes in traffic.

## 5. Sustainability Tech Application
*   **Scenario:** Processing large datasets of supply chain logs.
*   **Architecture:** S3 (Ingest) -> Lambda (Parse) -> DynamoDB (Store) -> SNS (Notify).
*   **Impact:** Reduces idle server costs and aligns with the sustainability mission.

## Interview Questions
1.  "Explain how you would handle a Lambda function that keeps timing out while processing a large SQS batch."
2.  "What are the pros and cons of using DynamoDB over a traditional PostgreSQL database for a sustainability tracking app?"
3.  "How do you implement 'Idempotency' in a serverless environment where a message might be delivered twice?"
4.  "How do you debug a performance issue in a distributed serverless system? (X-Ray, CloudWatch Insights)."
