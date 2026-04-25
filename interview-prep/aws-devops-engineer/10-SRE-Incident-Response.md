# SRE, Incident Response & Ownership

**Focus:** Production Operations, Incident Management, Developer Experience, Leadership

---

## Section A: Incident Response

### Q1: Incident Command
**Question:** 2 AM PagerDuty fires — API returning 500s for 30% of requests. Walk me through your first 15 minutes.

**Expected Answer:**
When paged at 2 AM for widespread 500 errors, my immediate goal is **mitigation, not root cause analysis**. The priority is stopping the bleeding.
*   **Minutes 0-2:** Acknowledge the page so escalations halt. I immediately open the high-level RED (Rate, Errors, Duration) dashboards to confirm the blast radius. Is it a single microservice or a total platform outage?
*   **Minutes 2-5:** I aggressively check for configuration drift. I check the CI/CD pipeline and Git logs for any deployments made in the last 2 hours. 80% of outages are caused by fresh deployments. If I correlate a recent release to the latency spike, I immediately trigger an automated rollback without waiting to debug the code.
*   **Minutes 5-10:** If it wasn't a deployment, I check the core infrastructure bottlenecks: Is the RDS CPU at 100%? Are EKS nodes evicting pods due to memory pressure? Is the API Gateway throttling requests?
*   **Minutes 10-15:** If the mitigation is not obvious, I declare an incident. I open a dedicated Slack incident channel, page the specific service owners for backup, and appoint an Incident Commander to handle communications, ensuring business stakeholders receive status updates every 15 minutes while the engineers focus on recovery.

---

### Q2: Blameless Post-Mortems
**Question:** What makes a good post-mortem?

**Expected Answer:**
A successful post-mortem must be rigorously **blameless**. The foundational assumption is that every engineer acts with good intentions based on the information they had. If a developer dropped the production database, the post-mortem does not ask "Why did John do this?", but rather "Why did our IAM policies, CI/CD checks, and database architecture allow a single command to cause catastrophic data loss?"

The document itself must follow a strict template: a minute-by-minute timeline of the incident, the quantifiable business impact, the root cause (discovered using the "5 Whys" iterative technique), and what went well (e.g., "our alerting caught the issue in 2 minutes"). However, a post-mortem is useless without Action Items. Every action item must be SMART (Specific, Measurable, Assignable, Realistic, Time-bound) and tracked in Jira. I mandate that all P1 incident action items are reviewed in a weekly engineering meeting to ensure they are actually prioritized in the sprint backlog, preventing the exact same outage from happening twice.

---

### Q3: Chaos Engineering
**Question:** How do you proactively test resilience?

**Expected Answer:**
Proactive resilience testing, or Chaos Engineering, is how we prove our high-availability architecture actually works before a real outage strikes. The process is strictly hypothesis-driven. We do not just break things randomly; we state a hypothesis, such as: "If we terminate 1 out of 3 EC2 worker nodes, the Auto Scaling Group will replace it within 3 minutes, and the application will experience zero HTTP 500 errors."

To execute this on AWS, I use AWS Fault Injection Service (FIS) to safely simulate infrastructure failures like API throttling, sudden RDS reboots, or complete Availability Zone blackouts. For Kubernetes-specific testing, I prefer Litmus Chaos to simulate network latency between pods or disk-fill scenarios. Operationally, I organize "Game Days"—scheduled, controlled exercises where engineers practice incident response while the chaos experiments run. Crucially, chaos engineering starts small in staging environments. We only graduate to executing faults in production once we have absolute confidence in our automated recovery and observability systems, and we always have an immediate "abort" button ready.

---

## Section B: Production Operations

### Q4: Disaster Recovery
**Question:** Design DR for EKS + RDS in us-east-1. What's your RTO/RPO?

**Expected Answer:**
Designing Disaster Recovery (DR) depends entirely on the business's budget and acceptable RTO (Recovery Time Objective) and RPO (Recovery Point Objective). 
For an **Active-Active** architecture, we deploy identical EKS clusters in `us-east-1` and `us-west-2`, using Route 53 latency-based routing to serve traffic from both simultaneously, backed by an Aurora Global Database. This achieves near-zero RTO and RPO, but doubles infrastructure costs.

More commonly, I implement a **Pilot Light** strategy. In the DR region (`us-west-2`), we maintain only the absolute core data layers: an asynchronous RDS cross-region read replica and an empty, scaled-to-zero EKS cluster defined in Terraform. If `us-east-1` goes down, the recovery procedure is: (1) Promote the RDS replica to a standalone primary database, (2) trigger the CI/CD pipeline to deploy applications and scale up the EKS worker nodes, and (3) update Route 53 to failover traffic. This yields an RPO of minutes, but an RTO of 30-60 minutes. The most critical rule of DR is that a plan is useless if untested; I mandate full, documented DR failover drills every quarter to ensure the runbooks are accurate.

---

### Q5: Capacity Planning
**Question:** How do you predict and plan for capacity needs?

**Expected Answer:**
Capacity planning requires bridging infrastructure metrics with business projections. Technically, I analyze trailing 90-day utilization trends for CPU, Memory, and Storage across the fleet. However, historical data isn't enough. I actively collaborate with product teams to correlate these metrics with leading business indicators—for example, if marketing expects a 20% increase in user signups next quarter, I translate that into required database IOPS and API request throughput.

To validate these projections, I run scheduled load testing using k6 or Artillery, simulating the expected peak traffic plus a 30% margin of error to ensure our Auto Scaling Groups trigger correctly under stress. In Kubernetes environments, capacity planning is enforced via Resource Quotas; I monitor namespace consumption heavily, triggering alerts when a team consumes 70% of their allocated cluster resources, forcing a capacity review before they hit the ceiling. Finally, I integrate tools like Infracost directly into the Terraform CI/CD pipeline to provide immediate, automated cost forecasting for any proposed infrastructure expansion before it is merged.

---

### Q6: Toil Reduction
**Question:** What is "toil" and how do you eliminate it?

**Expected Answer:**
In SRE, "toil" is strictly defined as manual, repetitive, tactically devoid work that scales linearly with service growth. If a human has to SSH into a server to clear disk space, manually rotate a database password, or manually review and approve routine staging deployments, that is toil. It leads to burnout and prevents engineers from doing high-value architectural work.

My primary goal is to ruthlessly eliminate toil, capping it at an absolute maximum of 50% of an SRE's time. I approach this systematically: manual secret rotation is replaced by AWS Secrets Manager Lambda rotators; IAM user creation is eliminated by federating AWS with Okta/Azure AD; and disk full alerts are mitigated by configuring aggressive ECR and EBS lifecycle policies. To manage this, I require engineers to tag Jira tickets as "Toil." During monthly sprint planning, we review the highest-frequency toil categories and explicitly dedicate engineering cycles to write the Python or Terraform automation required to eliminate that specific class of work forever.

---

## Section C: Developer Experience & Ownership

### Q7: Platform as a Product
**Question:** How do you measure and improve developer experience?

**Expected Answer:**
Treating the internal platform as a product means the software developers are our customers, and our goal is to maximize their velocity while minimizing friction. I measure this quantitatively using DORA metrics: tracking Deployment Frequency, Lead Time for changes, MTTR (Mean Time To Recovery), and Change Failure Rate. If Lead Time is high, our CI/CD pipelines are too slow or require too many manual approvals. I supplement this data qualitatively by running quarterly Developer NPS (Net Promoter Score) surveys to identify major pain points.

To improve the experience, I champion the concept of "Golden Paths." Instead of handing developers an empty AWS account, we provide an Internal Developer Portal (like Spotify's Backstage). A developer can click a button to bootstrap a new microservice. This automated action instantly provisions the GitHub repo, the Terraform scaffolding, a pre-configured GitLab CI/CD pipeline, and standardized Datadog observability dashboards. By providing these highly opinionated, secure-by-default templates, we eliminate the infrastructure cognitive load, allowing developers to focus entirely on writing business logic.

---

### Q8: Small Team Ownership
**Question:** How do you handle on-call and bus factor in a team of 3–5?

**Expected Answer:**
Managing a small engineering team requires aggressively mitigating the "bus factor"—the risk of a system failing because the only person who understands it is unavailable. First, no single engineer is allowed to "own" a critical system in isolation. We rotate sprint assignments specifically to force cross-training, ensuring at least two or three engineers have hands-on experience with every major architectural component.

For operational health, the on-call rotation must be sustainable; an engineer should never be primary on-call more frequently than 1-in-4 weeks to prevent burnout. I enforce a strict "No Runbook, No Alert" policy. If an alert fires and there isn't a documented procedure to handle it, the alert is disabled until the documentation is written. Furthermore, all significant infrastructure modifications must be executed via "Pair Operations"—two engineers reviewing the Terraform apply together. Finally, I mandate the use of Architecture Decision Records (ADRs) to permanently document *why* technical choices were made, preserving the context long after the original author has left the company.

---

### Q9: Technical Decision Making
**Question:** How do you choose between ECS Fargate and EKS and get team buy-in?

**Expected Answer:**
Navigating complex technical decisions requires moving away from opinions and anchoring the team in data and organizational context. When choosing between ECS Fargate and EKS, I start by drafting a formal Request for Comment (RFC) or Architecture Decision Record (ADR). This document objectively outlines the options, comparing them against strict criteria: current team expertise, long-term operational overhead, raw compute cost, and ecosystem integrations. 

Instead of endless architectural debates, I advocate for timeboxed, 3-day "Spikes." Two engineers build a minimal viable prototype using both technologies to uncover the hidden pain points of each. I then present the findings to the team. If ECS Fargate proves cheaper and requires less management, but a subset of the team prefers the popularity of Kubernetes, I foster an open debate, but ultimately drive toward a "Disagree and Commit" resolution to prevent analysis paralysis. Furthermore, I emphasize that as long as the application is strictly containerized, the compute orchestrator is a highly reversible decision, lowering the stakes of the choice.

---

## Section D: Behavioral Questions

### Q10: STAR Format Preparation

Prepare answers for:
1. **Conflict**: "...you disagreed with a senior engineer about architecture."
2. **Failure**: "...a deployment you led caused an outage."
3. **Impact**: "...you significantly improved reliability or productivity."
4. **Ownership**: "...you fixed a problem that wasn't your responsibility."
5. **Scaling**: "...you had to scale a system under unexpected load."

**STAR Format:**
- **S**ituation: Set the scene (team size, system, scale).
- **T**ask: Your specific responsibility.
- **A**ction: What YOU did (use "I", not "we").
- **R**esult: Quantify ("Reduced deploy time from 45 min to 8 min").
