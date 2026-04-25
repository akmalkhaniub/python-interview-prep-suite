# AWS Security: IAM, KMS & Secrets Manager

**Focus:** Security Best Practices, IAM, KMS, Secrets Management

---

## Section A: IAM Deep Dive

### Q1: Least Privilege Implementation
**Question:** How do you let developers debug production EKS pods without modifying infrastructure?

**Expected Answer:**
Implementing least privilege for developers accessing production EKS requires a dual-layered approach bridging AWS IAM and Kubernetes RBAC. First, the developer's IAM role must be heavily restricted. It should only allow read-only EKS actions like `eks:DescribeCluster` to allow them to authenticate, explicitly denying destructive actions like `eks:DeleteCluster`. I would then map this IAM role within the EKS `aws-auth` ConfigMap (or using EKS Access Entries) to a specific Kubernetes group.

Inside Kubernetes, I would bind this group to a heavily restricted `ClusterRole`. This role would only grant permissions for `get`, `list`, and `logs` on pods, strictly denying `create`, `delete`, and most importantly, `exec` permissions to prevent them from opening interactive shells into running containers. If interactive host-level debugging is truly required for an emergency, I strictly forbid SSH. Instead, I grant IAM permissions for AWS Systems Manager (SSM) Session Manager, which provides a secure, audited shell connection directly to the EC2 worker nodes without opening inbound ports, ensuring every keystroke is logged to an S3 bucket or CloudWatch.

---

### Q2: IRSA (IAM Roles for Service Accounts)
**Question:** What is IRSA and why is it better than node-level IAM roles?

**Expected Answer:**
IAM Roles for Service Accounts (IRSA) solves the massive security vulnerability of node-level IAM permissions. Historically, EC2 worker nodes required an IAM Instance Profile. The severe flaw with this model is that every single pod running on that node inherited those exact same AWS permissions, violating the principle of least privilege. If one low-priority pod was compromised, the attacker gained access to everything the node could touch.

IRSA leverages OpenID Connect (OIDC) federation to securely map AWS IAM roles directly to specific Kubernetes ServiceAccounts. The EKS cluster acts as an OIDC identity provider, and an IAM trust policy is configured to explicitly trust a specific namespace and ServiceAccount name (`system:serviceaccount:namespace:sa-name`). This allows a specific microservice—like a `payment-service` pod—to assume a scoped IAM role and receive temporary STS credentials to access a secure S3 bucket, while a neighboring pod on the exact same EC2 node receives absolutely no AWS permissions. Recently, AWS introduced EKS Pod Identity, which simplifies this architecture further by replacing the complex OIDC trust policies with a streamlined, native API.

---

### Q3: Permission Boundaries
**Question:** What are IAM Permission Boundaries and when do you use them?

**Expected Answer:**
An IAM Permission Boundary is an advanced feature used to delegate IAM management without risking privilege escalation. It is a managed policy that establishes the absolute *maximum* permissions an identity can hold. Even if an IAM Role is later granted full `AdministratorAccess` via its standard attached policies, the Permission Boundary ruthlessly truncates those permissions, ensuring the identity can only perform actions explicitly allowed by both the attached policy and the boundary.

This is critical in DevOps environments where developers need to create IAM roles for their own Lambda functions or ECS tasks. Without boundaries, a developer could maliciously create a role with admin rights and pass it to their application. By enforcing a Permission Boundary, security teams allow developers to create roles dynamically, but restrict those new roles from ever exceeding the boundary limits (e.g., restricting them to only accessing specific S3 buckets or DynamoDB tables). When combined with AWS Organizations Service Control Policies (SCPs) at the root level, Permission Boundaries provide a highly secure, defense-in-depth architecture.

---

## Section B: Encryption & KMS

### Q4: KMS Key Management
**Question:** How do you use KMS to encrypt data across RDS, S3, and EKS secrets?

**Expected Answer:**
Managing encryption at scale requires utilizing AWS Key Management Service (KMS) with Customer Managed Keys (CMKs) to ensure full control over key policies and rotation schedules. For Amazon RDS, encryption must be enabled at the time of database creation. If an existing database is unencrypted, the only migration path is taking a snapshot, copying the snapshot with encryption enabled, and restoring it as a new instance. For S3, I enforce SSE-KMS as the default bucket encryption, and implement a strict bucket policy denying any `s3:PutObject` request that doesn't explicitly specify KMS encryption headers.

In Kubernetes, standard secrets are merely base64 encoded. To secure them, I configure EKS to utilize KMS Envelope Encryption. This ensures that the Kubernetes API server encrypts the data encryption key (DEK) holding the secrets before persisting them to the backend `etcd` database. Operationally, I strictly isolate KMS key policies: separating "Key Administrators" (who can rotate or delete keys) from "Key Users" (the application roles that can only encrypt/decrypt). Finally, I always enable automatic annual key rotation, which generates new cryptographic material for future encryption while seamlessly retaining old versions to decrypt legacy data.

---

### Q5: Secrets Manager vs. Parameter Store

| Feature | Secrets Manager | SSM Parameter Store |
|---|---|---|
| Auto-rotation | Yes (Lambda-based) | No |
| Cross-account | Yes | Limited |
| Cost | $0.40/secret/month | Free (standard) |
| Best for | DB creds, API keys | Config values, feature flags |

**Expected Answer:**
While AWS Secrets Manager and SSM Parameter Store (SecureString) both encrypt data using KMS, they serve different architectural purposes. Secrets Manager is a premium service designed explicitly for high-value secrets like database credentials and third-party API keys. Its standout feature is native, automated secret rotation via Lambda functions, seamlessly updating RDS passwords without human intervention. It also natively supports complex cross-account resource policies.

SSM Parameter Store is primarily a configuration management service. While it can store secure strings for free, it lacks built-in rotation and robust cross-account sharing. Therefore, I use Parameter Store for non-sensitive configuration values or feature flags, and reserve Secrets Manager strictly for critical credentials due to its $0.40 per-secret monthly cost. To bridge these services into EKS, I deploy the External Secrets Operator, which securely fetches data from either AWS service at runtime and injects them into native Kubernetes Secret objects, keeping credentials entirely out of the Git repositories.

---

### Q6: Secrets in Kubernetes
**Question:** K8s Secrets are base64-encoded, not encrypted. How do you secure them?

**Expected Answer:**
A common misconception is that Kubernetes Secrets are inherently secure; in reality, they are simply base64-encoded plain text. To harden them, the first step is enabling EKS Envelope Encryption at the cluster level, which utilizes AWS KMS to encrypt the raw secret data at rest within the underlying `etcd` database. However, this doesn't solve the problem of how to get the secrets into the cluster securely without committing them to Git.

To solve the delivery problem, I prefer using the External Secrets Operator, which allows developers to define a custom resource pointing to an AWS Secrets Manager ARN. The operator dynamically fetches the secure payload and injects it into the cluster at runtime. Alternatively, for teams lacking AWS Secrets Manager, I use Bitnami Sealed Secrets, which allows developers to safely encrypt secrets locally using a public key before committing them to Git; only the private controller running inside the cluster can decrypt them. Regardless of the delivery method, strict Kubernetes RBAC must be enforced so that only the specific Pod's ServiceAccount has the `get` permission on its respective secret, and Kubernetes Audit Logging must be enabled to track precisely which user or service accessed the payload.

---

## Section C: Network Security

### Q7: WAF & DDoS Protection
**Question:** How do you protect a public API on EKS from web attacks and DDoS?

**Expected Answer:**
Protecting a public-facing EKS workload requires a multi-layered edge defense architecture. By default, attaching an Application Load Balancer (ALB) automatically enrolls the application in AWS Shield Standard, which absorbs massive Layer 3 and Layer 4 volumetric DDoS attacks (like UDP reflection or SYN floods) at the AWS network edge before they ever reach the VPC.

To protect against sophisticated Layer 7 application attacks, I place Amazon CloudFront in front of the ALB. CloudFront's globally distributed edge locations easily absorb HTTP floods. I then attach AWS WAF (Web Application Firewall) to CloudFront. WAF is configured with managed rule groups to block common vulnerabilities like SQL Injection (SQLi) and Cross-Site Scripting (XSS). Crucially, I configure strict rate-based WAF rules to throttle IP addresses that exceed expected request thresholds (e.g., 2,000 requests per 5 minutes), shutting down brute-force attacks and scraping bots before they consume expensive EKS compute resources.

---

### Q8: Security Scanning & Compliance
**Question:** How do you automate security scanning for containers and infrastructure?

**Expected Answer:**
Automating security scanning requires integrating specialized tools across every phase of the DevSecOps lifecycle. During the CI/CD pipeline, I mandate tools like Trivy or Snyk to ruthlessly scan Dockerfile layers for known CVEs and outdated dependencies before an image is ever built. Simultaneously, tools like Checkov or `tfsec` analyze the Terraform code statically to block misconfigurations—like publicly exposed S3 buckets—from being deployed. Once images are pushed to AWS ECR, native continuous scanning ensures we are alerted to newly discovered vulnerabilities.

At runtime, perimeter scanning is no longer enough. I deploy Falco as a DaemonSet within the EKS cluster. Falco acts as a behavioral threat detection engine using eBPF, alerting instantly if a container spawns an unexpected process (like `bash`), modifies a sensitive file (`/etc/shadow`), or opens an unusual outbound network connection. I also enable Amazon GuardDuty with EKS Audit Log Monitoring, which utilizes machine learning to detect compromised IAM credentials or anomalous API calls targeting the cluster. Finally, routine executions of `kube-bench` ensure the underlying worker nodes consistently comply with strict CIS security benchmarks.
