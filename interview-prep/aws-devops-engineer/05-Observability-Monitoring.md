# Observability, Monitoring & Logging

**Focus:** CloudWatch, Grafana, Prometheus, Splunk, Datadog, ELK

---

## Section A: Observability Foundations

### Q1: Three Pillars of Observability
**Question:** What are the three pillars? How do they complement each other?

**Expected Answer:**
- **Metrics**: Numeric time-series (CPU, latency, error rate). Cheap to store, good for alerting. Tool: Prometheus + Grafana.
- **Logs**: Structured event records. Rich context but expensive at scale. Tool: ELK, Splunk, CloudWatch.
- **Traces**: Request flow across services. Essential for microservices. Tool: OpenTelemetry, X-Ray, Jaeger.
- **Correlation**: Trace ID links a log entry to a metric spike to the exact distributed call chain.

---

### Q2: Prometheus in EKS
**Question:** How do you deploy Prometheus in EKS? How does service discovery work?

**Expected Answer:**
- **Deployment**: kube-prometheus-stack Helm chart. Creates Prometheus, Alertmanager, Grafana.
- **Service Discovery**: `ServiceMonitor` and `PodMonitor` CRDs auto-discover targets by labels.
- **Storage**: Local PVC short-term. Thanos or Cortex for long-term, multi-cluster.
- **AWS Managed**: Amazon Managed Prometheus (AMP) — fully managed, PromQL-compatible.

---

### Q3: Grafana Dashboard Design
**Question:** What dashboards do you set up on day one for a production EKS platform?

**Expected Answer:**
- **Cluster Overview**: Node count, CPU/memory utilization, pod counts by status.
- **Namespace Dashboard**: Per-namespace resource usage vs. quotas.
- **Application RED**: Rate (req/sec), Errors (5xx %), Duration (p50/p95/p99).
- **Node Health**: Disk/memory/PID pressure, network I/O.
- **Cost Dashboard**: Kubecost integration.

---

## Section B: Centralized Logging

### Q4: Log Architecture for EKS
**Question:** How do you collect logs from EKS pods and send them to a centralized system?

**Expected Answer:**
- **DaemonSet**: Fluent Bit (lightweight) or Fluentd on every node.
- **Pipeline**: Pod stdout → Node `/var/log/containers` → Fluent Bit → Elasticsearch/Splunk/CloudWatch.
- **Structured logging**: Apps must log in JSON: `timestamp`, `level`, `message`, `trace_id`, `service`.
- **Retention**: Hot (7 days), Warm (30 days), Cold (S3 archive).

---

### Q5: ELK vs. Splunk vs. Datadog
**Question:** Compare centralized logging platforms.

| Criteria | ELK (Self-managed) | Splunk | Datadog |
|---|---|---|---|
| Cost | Open-source + infra | Expensive per GB | Per-host pricing |
| Ops Overhead | High (manage ES) | Medium (Cloud) | Low (SaaS) |
| Search | KQL, Lucene | SPL (very powerful) | Faceted search |
| Best For | Budget-conscious | Enterprise compliance | Cloud-native |

---

### Q6: CloudWatch vs. Prometheus
**Question:** When do you use CloudWatch vs. Prometheus/Grafana?

**Expected Answer:**
- **CloudWatch**: Best for AWS-native (RDS, Lambda, ALB). Built-in, no setup.
- **Prometheus**: Best for K8s workloads, custom app metrics.
- **Coexistence**: CloudWatch for infra, Prometheus for apps. Grafana queries both as data sources.
- **Cost trap**: CloudWatch custom metrics and log ingestion get expensive at scale.

---

## Section C: Alerting & Tracing

### Q7: Alerting Strategy
**Question:** How do you design actionable alerts without alert fatigue?

**Expected Answer:**
- **Alert on symptoms**: "error rate > 5%" not "CPU > 80%".
- **Severity**: P1 (pages on-call), P2 (Slack), P3 (auto-ticket).
- **Every alert needs a runbook**: Link in alert annotation.
- **SLO-based**: Burn rate alerts ("At this rate, we breach SLO in 6 hours").
- **Review regularly**: If no one acts on an alert, delete or downgrade it.

---

### Q8: Distributed Tracing
**Question:** User reports slow page load. Request hits API GW → ALB → 3 EKS services → RDS. Find the bottleneck.

**Expected Answer:**
- **Trace ID propagation**: OpenTelemetry SDK, auto-injects `traceparent` header.
- **Collector**: OTel Collector DaemonSet → exports to X-Ray/Jaeger/Datadog.
- **Waterfall view**: See time per service. Identify the slow span.
- **RDS**: Enable Performance Insights to correlate slow queries with trace timestamps.

---

### Q9: SLOs, SLIs, and Error Budgets
**Question:** Define and explain how you use them in practice.

**Expected Answer:**
- **SLI (Indicator)**: The metric. "% of requests < 200ms."
- **SLO (Objective)**: The target. "99.9% of requests < 200ms over 30 days."
- **Error Budget**: `100% - 99.9% = 0.1%` allowable failures. If exhausted → freeze deploys, fix reliability.
- **Implementation**: Prometheus recording rules → Grafana burn rate dashboard → Alertmanager fires on high burn.
