# Event-Driven & Serverless Architecture

**Focus:** API Gateway, EventBridge, SQS/SNS, Step Functions, Lambda

---

## Section A: Event-Driven Architecture

### Q1: EventBridge vs. SQS vs. SNS
**Question:** How does EventBridge differ from SQS/SNS? When would you use each?

**Expected Answer:**
While SNS, SQS, and EventBridge all handle asynchronous messaging, their core architectural purposes differ significantly. **SNS (Simple Notification Service)** is a push-based Pub/Sub mechanism designed for high-throughput "fan-out." When a message is published, it pushes that identical payload to all subscribed endpoints simultaneously, making it ideal for broadcasting a single event (like an order placement) to multiple independent microservices. 

**SQS (Simple Queue Service)**, conversely, is a pull-based buffer. It decouples producers from consumers, holding messages durably until a consumer actively polls and processes them. SQS is strictly designed to handle backpressure and protect downstream databases from being overwhelmed. 

**EventBridge** is a modern, serverless Event Bus. Unlike SNS, which blindly broadcasts, EventBridge allows consumers to define complex, content-based routing rules. A microservice can subscribe to only receive events where `"detail.status" == "SHIPPED"`. It also natively integrates with hundreds of AWS services and third-party SaaS apps, and features a built-in Schema Registry.

| Use Case | Service |
|---|---|
| "Notify 5 services when order placed" | SNS |
| "Buffer tasks for a worker fleet" | SQS |
| "Route events by type/content to different targets" | EventBridge |
| "React to AWS service events" | EventBridge |

Architecturally, the most common enterprise pattern is "SNS to SQS Fan-Out." A producer publishes a single message to an SNS topic, which pushes it to multiple dedicated SQS queues. Each consuming microservice then pulls from its own queue, combining the broadcasting power of SNS with the fault-tolerance and retry capabilities of SQS.

---

### Q2: SQS Deep Dive
**Question:** Standard vs. FIFO? How do you handle Dead Letter Queues?

**Expected Answer:**
When designing an SQS architecture, the first decision is between Standard and FIFO (First-In-First-Out) queues. **Standard queues** provide nearly unlimited throughput but guarantee only "at-least-once" delivery and "best-effort" ordering. This means your consumer application *must* be strictly idempotent, as it may receive the same message twice or out of order. **FIFO queues** guarantee "exactly-once" processing and strict ordering, but are significantly throttled, supporting only 300 API calls per second (or up to 3,000 if aggressively batching).

For fault tolerance, every queue must be paired with a **Dead Letter Queue (DLQ)**. If a consumer fails to process a message successfully after a configured number of retries, SQS automatically moves it to the DLQ. Monitoring the depth of the DLQ is a critical P1 alert, as it indicates a broken consumer or poisonous message. Once the consumer bug is fixed, AWS provides a native DLQ Redrive task to easily replay the failed messages back to the source queue. Finally, performance tuning requires two settings: setting the "Visibility Timeout" generously higher than the maximum expected processing time (so messages don't prematurely reappear and get processed twice) and enabling "Long Polling" (`ReceiveWaitTimeSeconds = 20`) to drastically reduce empty API calls and lower AWS billing.

---

### Q3: Step Functions Orchestration
**Question:** Design a workflow: validate → charge → update inventory → confirm. Handle partial failures.

**Expected Answer:**
AWS Step Functions is the definitive choice for orchestrating complex, multi-step serverless workflows. To design an e-commerce checkout flow, I would utilize various state types: a `Task` state to invoke the validation Lambda, a `Choice` state to branch logic based on the validation result, and a `Parallel` state to charge the credit card and update the database simultaneously. 

Because distributed systems inevitably fail, the workflow must implement the **Saga Pattern**. If the "Charge Card" step succeeds, but the subsequent "Update Inventory" step fails, the workflow cannot simply stop. Every Step Function state must include a `Catch` block that intercepts the error and routes the execution to a compensating action—in this case, triggering a "Refund Card" Lambda to ensure the system remains eventually consistent. Architecturally, I would choose **Standard Workflows** for this process, as they provide full visual auditability and can run for up to a year, unlike **Express Workflows**, which are cheaper and faster but limited to 5-minute, fire-and-forget executions. Above all, because Step Functions guarantees "at-least-once" execution of tasks, every single Lambda function in the orchestration must be rigorously designed to be idempotent.

---

## Section B: API Gateway

### Q4: API Gateway Types
**Question:** Compare REST API, HTTP API, and WebSocket API.

**Expected Answer:**
AWS API Gateway offers three distinct flavors: REST, HTTP, and WebSocket. **REST APIs** represent the original, fully-featured offering. They provide comprehensive enterprise capabilities including request validation, response transformation, edge caching, native AWS WAF integration, and complex usage plans with API keys. However, this feature richness comes at a significantly higher cost and slightly higher latency.

**HTTP APIs** are the modern, streamlined alternative. They are up to 71% cheaper and offer lower latency, but achieve this by stripping out features like request validation and WAF integration. They do, however, feature a native, incredibly easy-to-configure OIDC/JWT authorizer. For most modern, internal microservices or simple public endpoints, HTTP APIs are my default choice. **WebSocket APIs** serve an entirely different architectural need, maintaining persistent, stateful, bidirectional connections between the client and server, essential for real-time applications like live chat or streaming financial dashboards. Regardless of the type, I heavily utilize VPC Links to securely route API Gateway traffic to private microservices residing in isolated subnets without exposing them to the internet.

---

### Q5: Throttling & Scaling
**Question:** Lambda behind API Gateway returns 429 errors during traffic spike. How do you fix it?

**Expected Answer:**
Resolving a 429 "Too Many Requests" error in a serverless architecture requires identifying which layer is throttling. First, I check API Gateway. By default, API Gateway imposes a soft limit of 10,000 requests per second. If the spike exceeded this, or if a specific API Stage or Usage Plan had a lower artificial throttle configured, the Gateway will reject the requests before they ever reach compute.

If the Gateway limits were not breached, the bottleneck is Lambda Concurrency. An AWS account has a default regional limit of 1,000 concurrent Lambda executions. If multiple functions are scaling simultaneously, they exhaust this shared pool, causing critical functions to be throttled. To fix this immediately, I would allocate **Reserved Concurrency** to the failing function, permanently carving out a slice of the account limit to guarantee it can scale. If the 429s are accompanied by high latency due to "cold starts," I would configure **Provisioned Concurrency** to keep a specific number of execution environments pre-warmed. Ultimately, if the API does not strictly require a synchronous response, the best architectural fix is placing an SQS queue between API Gateway and Lambda to absorb the spike and allow the function to process the backlog at its own pace.

---

## Section C: Serverless DevOps

### Q6: Lambda Deployment & Versioning
**Question:** How do you deploy Lambda safely with rollback?

**Expected Answer:**
Deploying serverless functions safely requires moving away from updating the `$LATEST` code directly. Instead, every deployment must publish an immutable Lambda **Version** (e.g., Version 42). I then utilize Lambda **Aliases**—which act like mutable pointers (e.g., a `prod` alias)—and configure API Gateway to exclusively route traffic to the alias, never a specific version number.

When deploying a new update (Version 43), I configure the `prod` alias to perform traffic shifting. I start by routing 90% of traffic to v42 and 10% to v43. I rely on AWS CodeDeploy to automate this Canary deployment process. CodeDeploy monitors pre-configured CloudWatch Alarms (tracking 5xx errors or high latency) over a specified baking period. If the alarms trigger, CodeDeploy automatically halts the deployment and instantaneously shifts 100% of the traffic back to v42. If the deployment remains healthy, it gradually shifts the remaining traffic to the new version, ensuring zero downtime and minimal customer blast radius during regressions.

---

### Q7: Serverless Observability
**Question:** What are unique observability challenges for serverless?

**Expected Answer:**
Serverless architectures introduce extreme observability challenges because the infrastructure is entirely ephemeral. You cannot SSH into a server, and you cannot run traditional daemon-based monitoring agents like Datadog or Splunk on the host OS. Furthermore, logic is heavily distributed; a single user click might asynchronously traverse an API Gateway, an SNS topic, three different Lambda functions, and a DynamoDB table.

To solve this, structured logging in JSON format is absolutely mandatory, ensuring every single log line across all services contains a unified `trace_id`. To visualize this distributed spaghetti, I instrument the code with AWS X-Ray or OpenTelemetry SDKs, which generate a visual map of the request execution and highlight the exact latency bottleneck. I also enable CloudWatch Lambda Insights to track memory profiling and compute efficiency. Finally, I create specific dashboards tracking `Init Duration` to mathematically quantify the impact of "cold starts" on user latency, and strictly monitor function execution duration, because in serverless, execution time directly equals financial cost.

---

## Section D: Streaming & Nice-to-Have

### Q8: Kinesis vs. MSK (Kafka)
**Question:** When do you pick Kinesis over MSK?

**Expected Answer:**
Choosing between Amazon Kinesis Data Streams and Amazon MSK (Managed Streaming for Apache Kafka) is a decision of operational complexity versus ecosystem power. **Kinesis** is the native, fully managed AWS solution. It is operationally simple, integrates seamlessly with Lambda and IAM out of the box, and scales linearly by manipulating the number of "Shards." If a team simply needs a highly durable buffer to ingest clickstream data or application logs before processing, Kinesis is my default recommendation. It becomes even more powerful when paired with Kinesis Data Firehose, which provides zero-code delivery of that streaming data directly into S3 or Redshift.

**Amazon MSK**, however, brings the immense power of the open-source Apache Kafka ecosystem. While MSK abstracts the server management, it still requires significant operational engineering regarding partition tuning, JVM memory management, and ZooKeeper/KRaft coordination. I choose MSK only when the architecture demands complex, multi-consumer event sourcing, strict CQRS patterns, or when the organization already possesses deep Kafka expertise and relies on tools like Kafka Connect or KSQL that Kinesis simply cannot replicate.

---

### Q9: Pixel Streaming Infrastructure
**Question:** How would you design Unreal Engine Pixel Streaming on AWS?

**Expected Answer:**
Designing a Pixel Streaming architecture for Unreal Engine on AWS revolves entirely around managing GPU costs and ensuring ultra-low latency WebRTC connections. The compute tier requires specialized EC2 GPU instances, typically the `g5` or `g5g` families equipped with NVIDIA hardware for hardware-accelerated video encoding. Because Pixel Streaming is notoriously a 1:1 ratio—requiring one dedicated Unreal Engine instance for every concurrent user session—auto-scaling must be extremely aggressive. The orchestration logic must instantly terminate idle EC2 instances the moment a user disconnects, as leaving powerful GPUs running idle will bankrupt a project in days.

Networking is equally critical. To establish the WebRTC peer-to-peer video stream, the infrastructure must host dedicated STUN and TURN servers (often using the open-source `coturn` project) to traverse restrictive corporate firewalls and NATs. To minimize the initial connection latency, I would route the signaling traffic through AWS Global Accelerator, utilizing Amazon's private fiber backbone rather than the public internet. Finally, to optimize network throughput between the signaling servers and the GPU fleet, all instances should ideally be launched within a Cluster Placement Group within a single Availability Zone.
