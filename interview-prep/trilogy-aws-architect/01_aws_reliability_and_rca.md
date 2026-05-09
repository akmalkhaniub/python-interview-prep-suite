# Deep Dive: AWS Reliability & The 5-Whys Methodology

Trilogy values engineers who can "restore order from instability" and provide genuine Root Cause Analyses (RCA).

## 1. The 5-Whys Process: A Practical Example

**Problem:** A critical SaaS product went down for 30 minutes.
1.  **Why?** The database hit its maximum connection limit.
2.  **Why?** A new container rollout caused a spike in initial connections.
3.  **Why?** The application doesn't use a connection pool and opens a new connection per request.
4.  **Why?** The legacy code (from an acquired product) was designed for low-traffic monoliths, not high-traffic containers.
5.  **Why? (Root Cause):** The infrastructure migration team roll-out plan didn't include connection-limit stress testing or RDS Proxy implementation.

**Permanent Fix:** Implement Amazon RDS Proxy to handle connection pooling and update the deployment runbook to include connection-limit validation.

## 2. Monitoring Edge Cases
Instead of just watching CPU/Memory, an AWS Architect at Trilogy constructs monitors for:
*   **Latency Outliers:** P99 latency spikes that affect high-value customers.
*   **API Throttling:** Detecting when the app hits AWS service limits before it crashes.
*   **Zombie Processes:** Monitoring container health beyond simple "running" status.
*   **Database Lock Contention:** Detecting when long-running transactions are blocking other queries.

## 3. Authoring "Copy-Paste-Ready" Runbooks
A Trilogy-grade runbook must include:
*   **Detection:** How to know the issue is happening (CloudWatch Link).
*   **Diagnosis:** Command-line steps (Bash/Python) to verify the problem.
*   **Remediation:** Exact commands to fix it.
*   **Verification:** How to prove the fix worked.
*   **Prevention:** The Jira/Ticket link for the permanent fix.

## 4. AI-Assisted System Discovery
When inheriting an unknown environment:
1.  **Crawl:** Use a Python script (Boto3) to list all resources across all regions.
2.  **Summarize:** Feed the list into an AI agent to identify "high-risk" areas (e.g., public S3 buckets, unencrypted databases).
3.  **Map:** Use the AI to generate a Mermaid diagram of the network topology (VPC, Subnets, Gateways).
4.  **Automate:** Generate Terraform code for the discovered resources to bring them under state management.

## Interview Questions
1.  "Describe a time you restored order to an undocumented, unstable AWS environment. What were your first 3 steps?"
2.  "What is the difference between a 'Temporary Remediation' and a 'Permanent Fix' in the context of an incident?"
3.  "How do you distinguish between an infrastructure failure you own and an application bug the engineering team owns?"
4.  "Explain how you would use AI tools to speed up your day-to-day administrative operations."
