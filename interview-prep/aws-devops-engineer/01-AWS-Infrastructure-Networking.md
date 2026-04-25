# AWS Infrastructure & Networking Deep Dive

**Focus:** Compute, Networking, Serverless Services

---

## Section A: VPC Architecture & Networking

### Q1: VPC Design Fundamentals
**Question:** You're tasked with designing a production VPC from scratch for a platform running EKS, RDS, and Lambda. Walk me through your subnet strategy, including public vs. private subnets, NAT Gateways, and AZ distribution.

**Expected Answer:**
To ensure high availability and fault tolerance, I would design the VPC across three Availability Zones (AZs). The network would be segmented into three distinct tiers. The public subnets would house resources that require direct internet access, such as Application Load Balancers (ALB), Network Load Balancers (NLB), NAT Gateways, and Bastion hosts. The application tier would reside in private subnets, hosting the EKS worker nodes and ECS tasks to ensure compute resources are isolated from direct internet access. Finally, the data tier would have its own deeply private subnets strictly for databases like RDS and ElastiCache, completely isolated from internet routing. 

For CIDR planning, I would allocate a `/16` block for the VPC (e.g., `10.0.0.0/16`) to provide ample IP addresses. The EKS private subnets would need substantial address space, such as `/19` or `/20`, because pod networking rapidly consumes IP addresses. I would deploy a NAT Gateway in each AZ to avoid cross-AZ data transfer costs and ensure fault tolerance, though a single NAT Gateway could be used if cost optimization is prioritized over strict high availability. Finally, I would enable VPC Flow Logs, streaming the network traffic data to S3 or CloudWatch for security auditing and network troubleshooting.

---

### Q2: Transit Gateway vs. VPC Peering
**Question:** When would you choose AWS Transit Gateway over VPC Peering? What are the trade-offs?

**Expected Answer:**
The choice between VPC Peering and AWS Transit Gateway comes down to scale, management complexity, and cost. VPC Peering establishes point-to-point connections and is ideal when you only have a few VPCs (typically 2 to 3) because it offers lower latency and avoids data processing charges. However, it lacks transitive routing, meaning you must create a full mesh of peering connections as the number of VPCs grows, which quickly becomes unmanageable.

In contrast, Transit Gateway acts as a central hub for a hub-and-spoke network topology. It supports transitive routing and significantly simplifies management by allowing hundreds or thousands of VPCs and on-premises networks to connect through a single gateway. While Transit Gateway introduces additional hourly costs and per-GB data processing charges, the operational simplicity is essential at scale. Furthermore, Transit Gateway route tables allow fine-grained traffic control, and it seamlessly integrates across multiple AWS accounts using the AWS Resource Access Manager (RAM).

---

### Q3: Security Groups vs. NACLs
**Question:** A junior engineer asks: "Why do we need both Security Groups and NACLs?" How do you explain the difference and when each matters?

**Expected Answer:**
Security Groups and Network Access Control Lists (NACLs) serve as complementary layers of defense in AWS, but they operate differently. Security Groups act as stateful, instance-level virtual firewalls. Being stateful means that if an inbound request is allowed, the return traffic is automatically permitted regardless of outbound rules. They only support "allow" rules, and all rules are evaluated simultaneously to determine access.

NACLs, on the other hand, act as stateless firewalls at the subnet level. Because they are stateless, both inbound and outbound rules must be explicitly defined for traffic to flow in both directions. Unlike Security Groups, NACLs process rules in numbered order from lowest to highest, and critically, they support explicit "deny" rules. As a best practice, Security Groups should be used as the primary mechanism for access control. NACLs are best utilized as a coarse, secondary defense layer, such as blocking specific known malicious IP addresses at the subnet boundary or enforcing strict compliance-driven explicit deny rules.

---

### Q4: DNS & Service Discovery
**Question:** How do you set up service discovery for microservices running across EKS and ECS in the same VPC?

**Expected Answer:**
To establish reliable service discovery across both EKS and ECS within the same VPC, I would utilize a combination of AWS Cloud Map, Route 53, and Kubernetes-native DNS. For ECS services, AWS Cloud Map provides native integration, automatically registering and deregistering task IP addresses in a fully managed service registry. Cloud Map handles the dynamic nature of ECS containers perfectly.

For the EKS workloads, Kubernetes uses CoreDNS for internal service resolution within the cluster. To enable cross-service communication so that an EKS pod can discover an ECS task, I would configure AWS Cloud Map to create a Route 53 Private Hosted Zone associated with the VPC. As long as the VPC has both `enableDnsSupport` and `enableDnsHostnames` turned on, the CoreDNS instances in EKS will forward unresolved queries to the Amazon-provided VPC DNS server. This allows EKS pods to resolve the ECS services registered in Cloud Map using standard DNS lookups, seamlessly bridging the two compute environments.

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

When selecting compute services, the decision hinges on operational overhead, execution duration, and ecosystem requirements. I would choose **AWS Lambda** for event-driven workloads, quick background processing, or API backends that execute in under 15 minutes. It scales instantly and offers the lowest operational overhead. 

If the workload requires containerization without the heavy lifting of cluster management, **ECS with Fargate** is the perfect middle-ground. It allows developers to run simple microservices with minimal operations. I would mandate **EKS (Elastic Kubernetes Service)** when the platform requires the rich Kubernetes ecosystem, such as advanced service meshes (Istio), complex network policies, or strict multi-cloud portability. Finally, raw **EC2 instances** are reserved for edge cases: legacy monoliths that require custom OS kernels, stateful applications that are hard to containerize, or heavily specialized GPU workloads for machine learning training.

---

### Q6: EC2 Instance Selection & Cost Optimization
**Question:** How do you choose the right EC2 instance family for EKS worker nodes? How do you optimize costs?

**Expected Answer:**
Choosing the right EC2 instances for EKS requires aligning node types with application profiles. I would categorize workloads and assign them to specific node groups: `m6i/m7g` for general-purpose applications, `c6i/c7g` for CPU-intensive tasks, `r6i` for memory-heavy caching systems, and `g5` for machine learning. Whenever possible, I highly recommend transitioning to AWS Graviton (ARM-based) instances like `m7g`, which can offer 20-40% better price performance, especially since containerized microservices are typically straightforward to compile for ARM architectures.

To aggressively optimize costs, I would implement a tiered capacity strategy. For the absolute baseline capacity required to keep the platform running securely, I would purchase Compute Savings Plans. For the elastic, scale-out layer hosting stateless web applications and background workers, I would heavily leverage Spot Instances, ensuring the applications are configured to handle the 2-minute interruption warnings gracefully via Kubernetes node termination handlers. Finally, I would continuously utilize AWS Compute Optimizer and tools like Kubecost to right-size instances based on actual usage metrics.

---

### Q7: Lambda Cold Starts & Limits
**Question:** A Lambda function behind API Gateway has inconsistent latency. How do you diagnose and fix cold start issues?

**Expected Answer:**
Lambda cold starts happen when AWS needs to spin up a new execution environment, which introduces latency. To resolve this, the most robust solution is enabling Provisioned Concurrency, which keeps a specified number of environments pre-warmed and ready to respond in double-digit milliseconds, though this incurs an additional cost. If the function is written in Java, I would immediately enable AWS SnapStart, which significantly reduces initialization time by taking a snapshot of the initialized environment and resuming from it.

I would also review the function's deployment package and runtime. Reducing the package size by stripping out unnecessary dependencies directly speeds up the download and instantiation phases. Additionally, lightweight runtimes like Node.js or Python typically experience much faster cold starts compared to Java or .NET. If the Lambda function is attached to a VPC, cold starts historically suffered due to ENI creation, but ensuring the architecture leverages the newer Hyperplane ENI improvements mitigates this. While using a scheduled CloudWatch Event to "ping" and warm the function is a common legacy trick, it is largely considered an anti-pattern today compared to using Provisioned Concurrency.

---

## Section C: Cross-Account & Multi-Account Strategy

### Q8: AWS Organizations & Landing Zone
**Question:** How would you structure a multi-account AWS environment for a growing platform team?

**Expected Answer:**
A robust, multi-account AWS structure should be built on the AWS Landing Zone framework utilizing AWS Organizations. The central **Management Account** must be strictly locked down, housing only consolidated billing and the organizational root to manage Service Control Policies (SCPs). It should not contain any application workloads. I would provision a dedicated **Security Account** to serve as the delegated administrator for services like GuardDuty and Security Hub, and to act as the immutable centralized vault for CloudTrail logs.

For infrastructure, a **Shared Services or Networking Account** is essential to centralize Transit Gateways, shared VPCs, and route 53 outbound resolver endpoints. The actual workloads must be isolated into distinct accounts for **Dev, Staging, and Production** to strictly contain blast radiuses. Additionally, a dedicated **CI/CD Account** would house GitLab runners and artifact repositories, assuming cross-account roles to deploy into the workload accounts. Finally, I would enforce global guardrails using SCPs across the organization, such as denying the creation of resources in unapproved geographic regions or preventing the disabling of critical security services.

---

### Q9: Cross-Account IAM Roles
**Question:** Your GitLab CI runner lives in a CI/CD account but needs to deploy Terraform to the production account. How do you set this up securely?

**Expected Answer:**
Secure cross-account deployments rely on the IAM `AssumeRole` pattern. I would create an IAM Role in the CI/CD account attached to the GitLab runner. In the Production account, I would create a distinct Deployment Role. The trust policy of this Production Deployment Role would explicitly allow the exact ARN of the CI/CD runner's IAM Role (`arn:aws:iam::CI_ACCOUNT:role/gitlab-runner`) to perform the `sts:AssumeRole` action. 

To maximize security, I would adhere to the principle of least privilege, ensuring the Production Deployment Role only has the exact permissions required by Terraform to execute its planned changes, rather than blanket administrative access. I would also restrict the session duration to the minimum necessary time, such as one hour. If the CI/CD system were a third-party SaaS rather than an internal account, I would mandate the use of an `ExternalId` in the trust policy to prevent the confused deputy problem. All cross-account assume role activities and subsequent infrastructure modifications will be natively audited in AWS CloudTrail for compliance.

---

### Q10: Cost Visibility Across Accounts
**Question:** How do you track and allocate cloud costs across multiple teams and accounts?

**Expected Answer:**
Achieving cost visibility at scale requires a combination of strict governance, native AWS tools, and specialized third-party solutions. The foundation is a rigid tagging strategy. I would use Terraform to programmatically apply tags like `team`, `environment`, and `project` to all supported resources, and optionally enforce this at the organizational level using Service Control Policies (SCPs) that block resource creation if these tags are missing. Once resources are tagged, I would activate these as Cost Allocation Tags in the Billing console, enabling granular breakdowns in AWS Cost Explorer across all linked accounts.

Proactive cost management is just as important as visibility. I would configure AWS Budgets for each account and project, establishing strict thresholds that trigger SNS notifications or Slack alerts to engineering teams when forecasted spends exceed expected limits. For specialized environments like EKS, where AWS native billing cannot see inside the cluster, I would deploy Kubecost to allocate costs down to the namespace or pod level. Additionally, integrating Infracost into the CI/CD pipeline ensures developers see estimated cost impacts directly on their Terraform Pull Requests before infrastructure is ever provisioned.

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
