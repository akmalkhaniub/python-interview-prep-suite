# Technical Study Guide: AWS Architect (Trilogy/Crossover)

## 1. AWS Reliability & Monitoring
- **Amazon CloudWatch:** 
    - Custom Metrics and Dashboards.
    - Anomaly Detection.
    - Logs Insights for high-speed troubleshooting.
- **AWS X-Ray:** Distributed tracing across containerized microservices.
- **Service Quotas & Limits:** Managing scalability across 50+ products.

## 2. Infrastructure as Code (IaC)
- **Terraform:**
    - State management (Remote state, Locking).
    - Modules for multi-tenant isolation.
    - Workspace management for 50+ different environments.
- **Ansible:** Configuration management for legacy servers and container orchestration.

## 3. Python for AWS (Boto3) Mastery
- **Boto3 Sessions & Clients:** Efficiently switching between accounts and regions.
- **Automated Remediation:** Writing scripts that trigger on CloudWatch Alarms to fix common issues (e.g., clearing disk space, restarting services).
- **Resource Discovery:** Using Python to crawl and document unknown VPCs, EC2s, and RDS instances.

## 4. Container Orchestration (ECS/EKS)
- **Scale:** Managing 100+ containers.
- **Deployment Strategies:** Blue/Green, Canary (10% rollout), and Rolling updates.
- **Health Checks:** Designing robust health checks that prevent traffic from hitting unhealthy containers.

## 5. Database Migration & Management
- **AWS DMS (Database Migration Service):**
    - Homogeneous vs. Heterogeneous migrations.
    - SCT (Schema Conversion Tool) for Oracle/MS-SQL to PostgreSQL.
- **Performance Tuning:** Identifying slow queries in MySQL and Postgres using RDS Performance Insights.

## 6. Root Cause Analysis (RCA) & Methodology
- **The 5-Whys:** A systematic approach to getting past symptoms to the root cause.
- **Runbooks:** Authoring "copy-paste-ready" markdown runbooks for incident remediation.
- **AI-Assisted Operations:** Using LLMs and agents to parse log files, generate Terraform, and suggest RCA hypotheses.

## Resources to Review
- [AWS Well-Architected Framework: Reliability Pillar](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html)
- [Boto3 Documentation: Best Practices](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [Terraform Up & Running (O'Reilly)](https://www.terraform.io/docs/language/index.html)
- [Google SRE Book: Incident Response](https://sre.google/sre-book/incident-response/)
- [AWS DMS Workshop: Heterogeneous Migrations](https://dms-workshop.com/)
