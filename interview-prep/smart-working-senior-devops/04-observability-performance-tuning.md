# Deep Dive: Observability & Performance Tuning

## 📊 The Monitoring Stack
*   **Prometheus:** Pull-based metric collection for K8s and infrastructure.
*   **Grafana:** Dashboarding for metrics.
*   **CloudWatch:** Native AWS metrics (EBS burst, ELK latency, RDS CPU).
*   **Datadog/Splunk/ELK:** (JD specific) For log aggregation and distributed tracing (APM).

## 💾 Data Service Optimization
*   **RDS (PostgreSQL):**
    *   **Performance Insights:** Visualizing load by wait events.
    *   **Autovacuum:** Tuning for high-write workloads.
    *   **Read Replicas:** Scaling read traffic.
*   **ClickHouse:** (JD specific) A column-oriented database for OLAP.
    *   **Indexing:** Tuning primary keys for query performance.
    *   **Storage:** Using EBS with high IOPS for faster data retrieval.

## 🛠️ Incident Response (SRE)
*   **Alerting:** Slack/PagerDuty integration with Prometheus Alertmanager.
*   **Error Budgets:** Tracking uptime vs. availability goals (SLAs/SLOs).
*   **MTTR (Mean Time To Recovery):** Automating rollbacks to reduce downtime.

## 💡 Interview Q&A
**Q: How do you optimize AWS costs for a large EKS cluster?**
**A:** 
1. Use **Spot Instances** for non-critical workloads via Karpenter.
2. Implement **Compute Savings Plans**.
3. Use **S3 Lifecycle Policies** to move old logs to Glacier.
4. Delete unattached EBS volumes and idle ALBs.

**Q: Describe a time you solved a performance bottleneck in a production system.**
**A:** (Example) "We noticed high RDS CPU utilization. I used **RDS Performance Insights** to identify a slow query missing an index. After applying the index via a Terraform-managed migration script, CPU dropped by 60% and application latency improved by 200ms."

## 🛠️ Code Snippet: Prometheus Alert Example
```yaml
groups:
- name: pod-alerts
  rules:
  - alert: PodFrequentRestart
    expr: increase(kube_pod_container_status_restarts_total[1h]) > 5
    for: 10m
    labels:
      severity: warning
    annotations:
      summary: "Pod {{ $labels.pod }} is restarting frequently"
```
