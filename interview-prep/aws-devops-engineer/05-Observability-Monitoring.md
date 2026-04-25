# Observability, Monitoring & Logging

**Focus:** CloudWatch, Grafana, Prometheus, Splunk, Datadog, ELK

---

## Section A: Observability Foundations

### Q1: Three Pillars of Observability
**Question:** What are the three pillars? How do they complement each other?

**Expected Answer:**
The three pillars of observability are Metrics, Logs, and Traces, and they must work together to provide a complete picture of system health. **Metrics** are numeric, time-series data points (like CPU usage, latency percentiles, or HTTP 500 error rates). Because they are mathematically aggregatable and cheap to store, they form the foundation of our alerting strategy, typically handled by Prometheus and visualized in Grafana. 

When a metric alert fires, engineers pivot to **Logs**, which provide rich, structured event records of exactly what an application was doing at that moment (often queried via ELK or Splunk). However, in a microservices architecture, a single user request might traverse a dozen services, making isolated logs hard to interpret. This is where **Traces** (captured via OpenTelemetry or AWS X-Ray) become essential. Tracing assigns a unique `Trace ID` to a request at the ingress point, propagating it down the stack. True observability is achieved through **Correlation**: attaching that `Trace ID` to every log line, allowing an engineer to see a metric spike, click into the exact error log, and instantly visualize the entire distributed request waterfall to pinpoint the failing microservice.

---

### Q2: Prometheus in EKS
**Question:** How do you deploy Prometheus in EKS? How does service discovery work?

**Expected Answer:**
To deploy Prometheus in an EKS cluster, I rely on the `kube-prometheus-stack` Helm chart. This bundles the Prometheus server, Alertmanager, and Grafana into a single, production-ready deployment. The true power of this stack lies in the Prometheus Operator, which handles dynamic service discovery via Custom Resource Definitions (CRDs) known as `ServiceMonitor` and `PodMonitor`. Developers simply deploy these CRDs alongside their applications, specifying label selectors, and Prometheus automatically discovers the pods and begins scraping their `/metrics` endpoints without any manual configuration changes.

For storage, a standard Prometheus deployment utilizes a local PersistentVolumeClaim (PVC), but this does not scale for long-term historical retention or multi-cluster aggregation. To solve this, I integrate Thanos or Cortex to push metric blocks to Amazon S3 for infinite, cheap storage. Alternatively, to reduce operational overhead entirely, I configure the cluster's Prometheus to act merely as a lightweight scraping agent, remote-writing the metrics directly to Amazon Managed Prometheus (AMP), a fully managed, highly available, PromQL-compatible service.

---

### Q3: Grafana Dashboard Design
**Question:** What dashboards do you set up on day one for a production EKS platform?

**Expected Answer:**
On day one of a production EKS platform, I deploy a tiered hierarchy of Grafana dashboards. At the top level is the **Cluster Overview**, displaying macroeconomic indicators like total node count, aggregated CPU/memory utilization, and critical alerts regarding pods stuck in `CrashLoopBackOff` or `Pending` states. Below this is the **Namespace Dashboard**, which provides granular visibility into individual team resource consumption tracked explicitly against their configured Kubernetes ResourceQuotas to prevent runaway costs.

For the application layer, I mandate standard **RED Method Dashboards** for every microservice, tracking the fundamental pillars of user experience: Rate (requests per second), Errors (percentage of 5xx HTTP codes), and Duration (latency visualized at the p50, p95, and p99 percentiles). At the infrastructure tier, a **Node Health Dashboard** monitors low-level EC2 metrics like disk I/O, memory pressure, and network saturation to preempt kernel-level evictions. Finally, an integrated **Cost Dashboard** powered by Kubecost translates these technical metrics into dollars and cents, enabling teams to understand the financial impact of their architecture.

---

## Section B: Centralized Logging

### Q4: Log Architecture for EKS
**Question:** How do you collect logs from EKS pods and send them to a centralized system?

**Expected Answer:**
Centralized logging in EKS is achieved using the sidecar or DaemonSet pattern. I deploy Fluent Bit (preferred over Fluentd due to its significantly lower memory footprint) as a DaemonSet, ensuring a log-forwarding agent runs on every single EC2 node. This agent continuously tails the `/var/log/containers` directory—where the Docker/containerd runtime writes standard output (stdout) and standard error (stderr) from every pod—enriching the logs with Kubernetes metadata (like namespace and pod name) before streaming them to a centralized backend like Elasticsearch, Splunk, or CloudWatch Logs.

For this pipeline to be searchable, strict engineering standards are required: all applications must output logs as structured JSON containing standardized keys such as `timestamp`, `level`, `message`, `trace_id`, and `service_name`. Unstructured text logs require expensive regex parsing and are prone to breaking. Finally, I implement a rigid lifecycle policy for log retention to control costs: keeping data in "Hot" searchable storage for 7 days, moving to "Warm" storage for 30 days, and eventually exporting to an Amazon S3 "Cold" archive to satisfy multi-year compliance requirements.

---

### Q5: ELK vs. Splunk vs. Datadog
**Question:** Compare centralized logging platforms.

**Expected Answer:**

| Criteria | ELK (Self-managed) | Splunk | Datadog |
|---|---|---|---|
| Cost | Open-source + infra | Expensive per GB | Per-host pricing |
| Ops Overhead | High (manage ES) | Medium (Cloud) | Low (SaaS) |
| Search | KQL, Lucene | SPL (very powerful) | Faceted search |
| Best For | Budget-conscious | Enterprise compliance | Cloud-native |

Selecting a centralized logging platform involves balancing cost, operational overhead, and search capabilities. A self-managed **ELK Stack (Elasticsearch, Logstash, Kibana)** is highly cost-effective regarding licensing, but managing a large-scale Elasticsearch cluster demands significant, dedicated operational engineering time, making it best for budget-conscious teams with strong infrastructure skills. **Splunk** is widely considered the enterprise gold standard, particularly for security and compliance, featuring an immensely powerful query language (SPL), but its strict per-GB ingestion pricing often becomes prohibitively expensive at scale. Finally, **Datadog** provides a fully SaaS, cloud-native experience with zero infrastructure overhead and excellent out-of-the-box Kubernetes integrations, but its per-host and custom-metric pricing models require strict governance to avoid surprise billing.

---

### Q6: CloudWatch vs. Prometheus
**Question:** When do you use CloudWatch vs. Prometheus/Grafana?

**Expected Answer:**
CloudWatch and Prometheus serve distinct, complementary roles in an AWS environment. **CloudWatch** is the definitive choice for monitoring AWS-native managed services like RDS, Lambda, and Application Load Balancers. Because it is deeply integrated into the AWS fabric, it requires zero installation and provides immediate, out-of-the-box telemetry. However, publishing millions of custom application metrics or ingesting terabytes of EKS pod logs into CloudWatch is notoriously expensive and often leads to massive billing surprises.

Conversely, **Prometheus** is the undisputed industry standard for Kubernetes workloads. Its pull-based architecture and powerful multi-dimensional data model (PromQL) are explicitly designed for highly dynamic, containerized environments. It handles high-cardinality custom application metrics incredibly efficiently and at a fraction of CloudWatch's cost. In a production environment, I utilize both: CloudWatch monitors the managed AWS infrastructure, Prometheus monitors the EKS cluster and microservices, and Grafana sits on top, querying both systems seamlessly as parallel data sources to provide unified observability.

---

## Section C: Alerting & Tracing

### Q7: Alerting Strategy
**Question:** How do you design actionable alerts without alert fatigue?

**Expected Answer:**
To combat alert fatigue, alerting must be strictly symptom-based rather than cause-based. I alert on user-facing symptoms (e.g., "HTTP 500 error rate exceeds 5%") rather than arbitrary resource thresholds (e.g., "CPU utilization is > 80%"). High CPU is not inherently an incident if the application is still serving requests quickly and successfully. Alerts must be strictly categorized by severity: P1 alerts page the on-call engineer via PagerDuty to wake them up, P2 alerts notify a Slack channel for business-hours attention, and P3 alerts automatically generate Jira tickets for the backlog.

Crucially, every single alert must include a link to a detailed, maintained Runbook in its annotation. If an alert fires and the engineer doesn't know what to do, the alert is useless. The most mature strategy is transitioning to SLO-based "Burn Rate" alerting, which triggers notifications only when the system is consuming its error budget at a pace that threatens the monthly SLA. Finally, I enforce a strict "alert hygiene" policy: if a specific alert fires three times and the on-call engineer ignores it or closes it without action, that alert must be downgraded or permanently deleted.

---

### Q8: Distributed Tracing
**Question:** User reports slow page load. Request hits API GW → ALB → 3 EKS services → RDS. Find the bottleneck.

**Expected Answer:**
To trace a slow request across a complex distributed system, I rely on distributed tracing instrumentation using the OpenTelemetry SDK. The SDK automatically intercepts incoming HTTP requests, generates a unique Trace ID, and injects a `traceparent` header into all downstream network calls. As the request flows from the API Gateway, through the ALB, and across the three EKS microservices, each component emits "spans" detailing its execution time back to an OpenTelemetry Collector DaemonSet, which forwards the data to a backend like AWS X-Ray or Datadog.

When a user reports a slow page load, I query the trace backend for their specific session or look for traces with high latencies. The tracing UI provides a Waterfall visualization of the entire call chain. If the delay is caused by the database, the specific microservice's span will show a long execution time on the SQL query execution step. I would then pivot to AWS RDS Performance Insights, aligning the exact timestamp from the trace to identify if the database was suffering from lock contention, high CPU, or if that specific SQL query simply lacked an appropriate index.

---

### Q9: SLOs, SLIs, and Error Budgets
**Question:** Define and explain how you use them in practice.

**Expected Answer:**
Service Level Indicators (SLIs), Objectives (SLOs), and Error Budgets form the mathematical foundation of Site Reliability Engineering. An **SLI** is the exact metric you are measuring, such as the percentage of HTTP requests that complete in under 200 milliseconds. An **SLO** is the internal target you set for that indicator, such as demanding that "99.9% of requests must complete in under 200ms evaluated over a rolling 30-day window." 

The **Error Budget** is simply 100% minus your SLO (in this case, 0.1%). This budget represents the acceptable amount of unreliability, giving the engineering team mathematical "permission" to take risks and deploy new features. However, if bad deployments exhaust the error budget, the consequence is a strict feature freeze; the team must halt all feature work and focus entirely on reliability engineering until the rolling 30-day window recovers. I implement this using Prometheus recording rules to track the SLI, Grafana dashboards to visualize the remaining Error Budget, and Alertmanager to trigger pages if the "burn rate" of the budget becomes dangerously high.
