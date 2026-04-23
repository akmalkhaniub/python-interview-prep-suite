# Event-Driven & Serverless Architecture

**Focus:** API Gateway, EventBridge, SQS/SNS, Step Functions, Lambda

---

## Section A: Event-Driven Architecture

### Q1: EventBridge vs. SQS vs. SNS
**Question:** How does EventBridge differ from SQS/SNS? When would you use each?

**Expected Answer:**
- **SNS**: Pub/Sub fan-out. One message → many subscribers. Push-based.
- **SQS**: Queue. Decouples producer/consumer. Pull-based. Guaranteed delivery.
- **EventBridge**: Event bus with content-based routing rules. Schema registry. Cross-account.

| Use Case | Service |
|---|---|
| "Notify 5 services when order placed" | SNS |
| "Buffer tasks for a worker fleet" | SQS |
| "Route events by type/content to different targets" | EventBridge |
| "React to AWS service events" | EventBridge |

- **Pattern**: SNS → SQS fan-out (combine pub/sub with queue durability).

---

### Q2: SQS Deep Dive
**Question:** Standard vs. FIFO? How do you handle Dead Letter Queues?

**Expected Answer:**
- **Standard**: At-least-once, best-effort ordering. Unlimited throughput.
- **FIFO**: Exactly-once, strict ordering. 300 msgs/sec (3000 with batching).
- **DLQ**: Messages failing N times move to DLQ. Monitor DLQ length as critical alert.
- **Redrive**: `StartMessageMoveTask` API to replay from DLQ.
- **Visibility timeout**: Must exceed processing time, else message reappears.
- **Long polling**: `ReceiveWaitTimeSeconds = 20` to reduce empty calls.

---

### Q3: Step Functions Orchestration
**Question:** Design a workflow: validate → charge → update inventory → confirm. Handle partial failures.

**Expected Answer:**
- **State types**: Task, Choice, Parallel, Wait, Map.
- **Error handling**: `Catch` block per state → compensation logic.
- **Saga pattern**: Each step has a compensating action. If `charge` succeeds but `inventory` fails → `refund`.
- **Express vs. Standard**: Express for high-volume < 5 min. Standard for long-running, auditable.
- **Idempotency**: Each Lambda must be idempotent (use idempotency key).

---

## Section B: API Gateway

### Q4: API Gateway Types
**Question:** Compare REST API, HTTP API, and WebSocket API.

**Expected Answer:**
- **REST API**: Full-featured (validation, caching, WAF, usage plans). Higher cost.
- **HTTP API**: Cheaper, faster, simpler. JWT authorizer built-in. Best for most new APIs.
- **WebSocket API**: Real-time bidirectional (chat, live dashboards).
- **Choice**: HTTP API unless you need REST-specific features.
- **Integration**: Lambda proxy, HTTP proxy, VPC Link for private backends.

---

### Q5: Throttling & Scaling
**Question:** Lambda behind API Gateway returns 429 errors during traffic spike. How do you fix it?

**Expected Answer:**
- **Lambda concurrency**: Default 1000/region. Request quota increase.
- **Reserved concurrency**: Guarantee capacity for critical functions.
- **Provisioned concurrency**: Pre-warm for predictable traffic.
- **API Gateway**: Default 10,000 req/sec. Configure per-stage/method limits.
- **Pattern**: SQS in front of Lambda for async processing — absorbs spikes.

---

## Section C: Serverless DevOps

### Q6: Lambda Deployment & Versioning
**Question:** How do you deploy Lambda safely with rollback?

**Expected Answer:**
- **Versions**: Each deploy publishes immutable version.
- **Aliases**: `prod` → version 42. Deploy v43, shift traffic gradually.
- **Weighted aliases**: 90% v42, 10% v43 (canary). Monitor. Shift to 100%.
- **CodeDeploy**: Automates canary with automatic rollback on CloudWatch alarm.
- **Rollback**: Repoint alias to previous version (instant).

---

### Q7: Serverless Observability
**Question:** What are unique observability challenges for serverless?

**Expected Answer:**
- **Ephemeral**: No persistent process for monitoring agents.
- **Cold starts**: Track with `Init Duration` in CloudWatch.
- **Distributed**: Single request may invoke 5 Lambdas + 2 queues + Step Function.
- **Solutions**: X-Ray/OTel for traces, structured logging, CloudWatch Lambda Insights.
- **Cost**: Monitor per-function invocation count and duration.

---

## Section D: Streaming & Nice-to-Have

### Q8: Kinesis vs. MSK (Kafka)
**Question:** When do you pick Kinesis over MSK?

**Expected Answer:**
- **Kinesis**: Fully managed, simple, AWS-native. Shard-based scaling.
- **MSK**: Complex event streaming, multi-consumer, existing Kafka ecosystem.
- **Firehose**: Zero-code delivery to S3, Redshift, ES. Best for log aggregation.
- **Choice**: Kinesis for simple ingestion. MSK for event sourcing, CQRS.

---

### Q9: Pixel Streaming Infrastructure
**Question:** How would you design Unreal Engine Pixel Streaming on AWS?

**Expected Answer:**
- **GPU instances**: g5/g5g with NVIDIA. Spot for stateless sessions.
- **Networking**: Low-latency — Global Accelerator or CloudFront for WebRTC signaling.
- **Scaling**: One Unreal instance per session. Scale GPU fleet on concurrent users.
- **TURN/STUN**: Deploy coturn for NAT traversal.
- **Cost**: Shutdown idle sessions aggressively. Placement groups for GPU locality.
