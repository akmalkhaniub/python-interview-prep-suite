# SRE, Incident Response & Ownership

**Focus:** Production Operations, Incident Management, Developer Experience, Leadership

---

## Section A: Incident Response

### Q1: Incident Command
**Question:** 2 AM PagerDuty fires — API returning 500s for 30% of requests. Walk me through your first 15 minutes.

**Expected Answer:**
- **Min 0–2**: Acknowledge. Check dashboard. Confirm impact (which services, which customers).
- **Min 2–5**: Check recent deployments (`git log --since='2 hours ago'`). Correlate timestamps.
- **Min 5–10**: Deployment-related → rollback. Otherwise → check RDS CPU, EKS nodes, SQS DLQ.
- **Min 10–15**: Not obvious → escalate. Page service owner. Start Slack incident channel.
- **Communication**: Status update every 15 min.
- **Principle**: Mitigate first, root-cause later.

---

### Q2: Blameless Post-Mortems
**Question:** What makes a good post-mortem?

**Expected Answer:**
- **Template**: Timeline, Impact, Root Cause, Contributing Factors, What Went Well, Action Items.
- **Blameless**: Focus on systems, not people.
- **Action items**: SMART (Specific, Measurable, Owner, Due Date).
- **Follow-up**: Review in weekly meeting. Track completion rate.
- **5 Whys**: Drill into root cause iteratively.

---

### Q3: Chaos Engineering
**Question:** How do you proactively test resilience?

**Expected Answer:**
- **AWS FIS**: Inject faults into EC2, EKS, RDS. Simulate AZ failure.
- **Litmus Chaos**: K8s-native. Pod kill, network delay, disk fill.
- **Game Days**: Scheduled exercises for incident practice.
- **Start small**: Non-production first. Graduate to production with rollback ready.
- **Hypothesis-driven**: "If we kill 1 of 3 nodes, we expect zero user impact."

---

## Section B: Production Operations

### Q4: Disaster Recovery
**Question:** Design DR for EKS + RDS in us-east-1. What's your RTO/RPO?

**Expected Answer:**
- **Pilot Light**: Minimal infra in us-west-2 (RDS cross-region replica, pre-provisioned EKS). RTO: 30–60 min. RPO: minutes.
- **Warm Standby**: Scaled-down but running. RTO: 10–15 min.
- **Active-Active**: Both regions serve traffic (Route 53 latency routing). RTO: ~0.
- **Key steps**: Promote RDS replica → scale EKS → update Route 53 → verify.
- **Test DR quarterly**: Actually failover. Don't just document it.

---

### Q5: Capacity Planning
**Question:** How do you predict and plan for capacity needs?

**Expected Answer:**
- **Trailing metrics**: CPU/memory trends over 30/90 days.
- **Growth projections**: Correlate with business metrics (signups, API calls).
- **Load testing**: k6, Locust, or Artillery monthly.
- **Headroom**: 30–40% for spikes. Auto-scaling handles rest.
- **EKS**: Track namespace usage vs. quotas. Alert at 70%.
- **Cost forecasting**: Cost Explorer + Infracost for planned TF changes.

---

### Q6: Toil Reduction
**Question:** What is "toil" and how do you eliminate it?

**Expected Answer:**
- **Toil**: Manual, repetitive, automatable work. Examples:
  - Rotating secrets → Secrets Manager rotation Lambda
  - Approving staging deploys → auto-approve with passing tests
  - Creating IAM users → SSO with Okta/Azure AD
  - Cleaning Docker images → ECR lifecycle policy
- **Goal**: Keep toil below 50% of SRE's time.
- **Track**: Log toil tasks. Review monthly for automation.

---

## Section C: Developer Experience & Ownership

### Q7: Platform as a Product
**Question:** How do you measure and improve developer experience?

**Expected Answer:**
- **DORA metrics**: Deployment Frequency, Lead Time, MTTR, Change Failure Rate.
- **Developer survey**: Quarterly NPS on platform tools.
- **Self-service**: Internal Developer Portal (Backstage) for creating services/envs.
- **Golden paths**: Opinionated templates with CI/CD, monitoring, IaC built-in.
- **Documentation**: Runbooks, ADRs, examples — not just wiki pages.

---

### Q8: Small Team Ownership
**Question:** How do you handle on-call and bus factor in a team of 3–5?

**Expected Answer:**
- **On-call**: Rotate weekly. Max 1-in-3 weeks. No solo on-call for critical systems.
- **Runbooks**: Every alert has one. Fix something undocumented → write runbook first.
- **Pair operations**: Critical changes done in pairs.
- **ADRs**: Document WHY decisions were made.
- **Cross-train**: Rotate who works on what. No single owner for any system.

---

### Q9: Technical Decision Making
**Question:** How do you choose between ECS Fargate and EKS and get team buy-in?

**Expected Answer:**
- **RFC/ADR**: Context, Options, Pros/Cons, Recommendation.
- **Criteria**: Team expertise, operational overhead, ecosystem, cost.
- **Prototype**: Spike both options for 2–3 days.
- **Decision**: Present findings. Disagree and commit if needed.
- **Reversibility**: Prefer reversible decisions. Containerization is the constant.

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
