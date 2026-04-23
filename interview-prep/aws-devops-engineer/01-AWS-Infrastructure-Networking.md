# AWS Infrastructure & Networking Deep Dive

**Focus:** Compute, Networking, Serverless Services

---

## Section A: VPC Architecture & Networking

### Q1: VPC Design Fundamentals
**Question:** You're tasked with designing a production VPC from scratch for a platform running EKS, RDS, and Lambda. Walk me through your subnet strategy, including public vs. private subnets, NAT Gateways, and AZ distribution.

**Expected Answer:**
- **3-AZ deployment** for high availability.
- **Public subnets**: ALB/NLB, NAT Gateways, Bastion hosts (if needed).
- **Private subnets (App Tier)**: EKS worker nodes, ECS tasks.
- **Private subnets (Data Tier)**: RDS, ElastiCache — no internet access.
- **CIDR Planning**: Use `/16` for VPC (e.g., `10.0.0.0/16`), `/20` or `/19` per subnet for EKS (IPs exhaust fast with pod networking).
- **NAT Gateway**: One per AZ for fault tolerance vs. single NAT for cost savings.
- **VPC Flow Logs**: Enable to S3 or CloudWatch for audit/troubleshooting.

---

### Q2: Transit Gateway vs. VPC Peering
**Question:** When would you choose AWS Transit Gateway over VPC Peering? What are the trade-offs?

**Expected Answer:**
- **VPC Peering**: Point-to-point, no transitive routing, free data transfer within same AZ. Best for 2–3 VPCs.
- **Transit Gateway**: Hub-and-spoke model, supports transitive routing, route tables per attachment, scales to hundreds of VPCs. Costs per GB processed.
- **Key trade-off**: Transit Gateway adds cost but simplifies management at scale. Peering has lower latency.
- **Multi-account**: Transit Gateway supports cross-account via AWS RAM (Resource Access Manager).

---

### Q3: Security Groups vs. NACLs
**Question:** A junior engineer asks: "Why do we need both Security Groups and NACLs?" How do you explain the difference and when each matters?

**Expected Answer:**
- **Security Groups**: Stateful, instance-level, allow-only rules, evaluated as a whole.
- **NACLs**: Stateless, subnet-level, support deny rules, evaluated in order.
- **Use case for NACLs**: Blocking known malicious IPs at the subnet boundary, compliance requirements for explicit deny.
- **Best Practice**: Use SGs as the primary mechanism; NACLs as a coarse secondary layer.

---

### Q4: DNS & Service Discovery
**Question:** How do you set up service discovery for microservices running across EKS and ECS in the same VPC?

**Expected Answer:**
- **Route 53 Private Hosted Zones** for custom DNS within VPC.
- **AWS Cloud Map** for service discovery (integrates with ECS natively).
- **CoreDNS** in EKS for internal Kubernetes service resolution.
- **Cross-service**: EKS pods can resolve Cloud Map services via Route 53; use VPC DNS settings (`enableDnsSupport`, `enableDnsHostnames`).

---

## Section B: Compute Services Comparison

### Q5: EC2 vs. ECS vs. EKS vs. Lambda — When to Use What?
**Question:** Your team is deploying a new microservice. How do you decide between EC2, ECS (Fargate), EKS, and Lambda?

**Expected Answer:**

| Criteria | EC2 | ECS Fargate | EKS | Lambda |
|---|---|---|---|---|
| Control | Full OS access | Container-level | Full K8s API | Function-level |
| Scaling | ASG (minutes) | Task-level (seconds) | HPA/Karpenter | Instant (ms) |
| Cost Model | Per-instance | Per vCPU/mem/sec | Nodes + control plane | Per-invocation |
| Best For | Legacy apps, GPU | Simple containers | Complex microservices | Event-driven, glue |
| Ops Overhead | Highest | Low | Medium-High | Lowest |

- **Lambda**: Ideal for < 15 min execution, event-driven triggers (S3, SQS, API GW).
- **ECS Fargate**: Good middle-ground when you don't need K8s complexity.
- **EKS**: When you need portability, complex networking (service mesh), or the K8s ecosystem.
- **EC2**: Legacy workloads, custom kernels, GPU-heavy (ML training).

---

### Q6: EC2 Instance Selection & Cost Optimization
**Question:** How do you choose the right EC2 instance family for EKS worker nodes? How do you optimize costs?

**Expected Answer:**
- **Instance families**: `m6i/m7g` (general), `c6i/c7g` (compute), `r6i` (memory), `g5` (GPU).
- **Graviton (ARM)**: 20–40% cheaper, excellent for containerized workloads.
- **Spot Instances**: Use for stateless workloads with proper termination handling (2-min warning).
- **Savings Plans/Reserved**: For baseline capacity.
- **Right-sizing**: Use AWS Compute Optimizer and Kubecost for recommendations.

---

### Q7: Lambda Cold Starts & Limits
**Question:** A Lambda function behind API Gateway has inconsistent latency. How do you diagnose and fix cold start issues?

**Expected Answer:**
- **Provisioned Concurrency**: Pre-warms execution environments (costs more).
- **SnapStart** (Java): Snapshots initialized state.
- **VPC Lambda**: Cold starts are worse in VPC — use Hyperplane ENIs (improved since 2019).
- **Package size**: Smaller deployment packages = faster cold starts.
- **Runtime choice**: Python/Node.js cold-start faster than Java/.NET.
- **Keep-alive tricks**: Scheduled CloudWatch Events to ping the function (anti-pattern but common).

---

## Section C: Cross-Account & Multi-Account Strategy

### Q8: AWS Organizations & Landing Zone
**Question:** How would you structure a multi-account AWS environment for a growing platform team?

**Expected Answer:**
- **Management Account**: Billing, Organizations, SCPs only.
- **Security Account**: GuardDuty, Security Hub, CloudTrail aggregation.
- **Shared Services / Networking**: Transit Gateway, DNS, shared VPCs.
- **Dev / Staging / Production**: Separate accounts per environment.
- **CI/CD Account**: GitLab runners, artifact storage, cross-account deploy roles.
- **SCPs**: Enforce guardrails (e.g., deny `ec2:RunInstances` outside approved regions).

---

### Q9: Cross-Account IAM Roles
**Question:** Your GitLab CI runner lives in a CI/CD account but needs to deploy Terraform to the production account. How do you set this up securely?

**Expected Answer:**
- **AssumeRole pattern**: CI/CD account role assumes a deployment role in the prod account.
- **Trust policy** on prod role: `arn:aws:iam::CI_ACCOUNT:role/gitlab-runner`.
- **External ID**: Add for third-party confusion deputy protection.
- **Session duration**: Keep short (1 hour max).
- **Least privilege**: Prod role only has permissions needed for `terraform apply`.
- **Audit**: CloudTrail logs show which account/role performed actions.

---

### Q10: Cost Visibility Across Accounts
**Question:** How do you track and allocate cloud costs across multiple teams and accounts?

**Expected Answer:**
- **AWS Cost Explorer** with linked accounts.
- **Tagging strategy**: Enforce `team`, `environment`, `project` tags via SCPs and Terraform.
- **Cost Allocation Tags**: Activate in billing.
- **Budgets & Alerts**: Per-account budgets with SNS notifications.
- **Third-party**: Kubecost for K8s-level cost attribution, Infracost for Terraform PR cost estimates.

---

## Section D: Hands-On Coding Challenge

### Challenge: Describe Your VPC in Terraform
**Task:** Write Terraform to create a production-ready VPC with public and private subnets across 3 AZs.

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}

resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr  # e.g. 10.0.0.0/16
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = "${var.env}-vpc"
    "kubernetes.io/cluster/${var.cluster_name}" = "shared"
  }
}

resource "aws_subnet" "private" {
  count             = 3
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 4, count.index)
  availability_zone = data.aws_availability_zones.available.names[count.index]
  tags = {
    Name = "${var.env}-private-${count.index}"
    "kubernetes.io/role/internal-elb" = "1"
  }
}
```

**Follow-up**: How would you make this a reusable Terraform module that works across dev/staging/prod?
