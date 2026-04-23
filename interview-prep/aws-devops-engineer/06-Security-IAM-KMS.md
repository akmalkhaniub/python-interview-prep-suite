# AWS Security: IAM, KMS & Secrets Manager

**Focus:** Security Best Practices, IAM, KMS, Secrets Management

---

## Section A: IAM Deep Dive

### Q1: Least Privilege Implementation
**Question:** How do you let developers debug production EKS pods without modifying infrastructure?

**Expected Answer:**
- **IAM Policy**: Allow `eks:DescribeCluster`, deny `eks:DeleteCluster`, `eks:UpdateClusterConfig`.
- **K8s RBAC**: ClusterRole with `get`, `list`, `logs`. No `create`, `delete`, `exec`.
- **aws-auth ConfigMap**: Map IAM role to K8s RBAC group.
- **SSM Session Manager**: Allow `ssm:StartSession` instead of SSH. Logged and auditable.
- **Conditions**: Restrict by source IP, MFA, or time window.

---

### Q2: IRSA (IAM Roles for Service Accounts)
**Question:** What is IRSA and why is it better than node-level IAM roles?

**Expected Answer:**
- **Node-level**: ALL pods on node get same permissions. Over-privileged.
- **IRSA**: Each pod gets its own IAM role via K8s ServiceAccount + OIDC federation.
- **How**: EKS OIDC provider → IAM trust policy trusts `system:serviceaccount:namespace:sa-name` → Pod gets STS credentials.
- **Example**: Only `payment-service` pod can access the `payments` S3 bucket.
- **Alternative**: EKS Pod Identity (newer, simpler).

---

### Q3: Permission Boundaries
**Question:** What are IAM Permission Boundaries and when do you use them?

**Expected Answer:**
- **Definition**: A managed policy that sets MAXIMUM permissions regardless of attached policies.
- **Use case**: Let developers create IAM roles (for Lambda, ECS) without privilege escalation.
- **Combined with SCPs**: SCPs at org level + Boundaries at account level = defense in depth.

---

## Section B: Encryption & KMS

### Q4: KMS Key Management
**Question:** How do you use KMS to encrypt data across RDS, S3, and EKS secrets?

**Expected Answer:**
- **CMK**: Full control over rotation, policies, deletion schedule.
- **RDS**: Encrypt at creation. Cannot encrypt existing unencrypted DB (snapshot → copy encrypted → restore).
- **S3**: Default SSE-KMS. Bucket policy can deny unencrypted `PutObject`.
- **EKS Secrets**: Envelope encryption — etcd stores secrets encrypted with KMS-managed DEK.
- **Key policy**: Separate key admins from key users.
- **Rotation**: Enable automatic annual rotation. Old versions retained for decryption.

---

### Q5: Secrets Manager vs. Parameter Store

| Feature | Secrets Manager | SSM Parameter Store |
|---|---|---|
| Auto-rotation | Yes (Lambda-based) | No |
| Cross-account | Yes | Limited |
| Cost | $0.40/secret/month | Free (standard) |
| Best for | DB creds, API keys | Config values, feature flags |

- **K8s integration**: External Secrets Operator syncs AWS secrets → K8s Secrets.
- **RDS rotation**: Built-in Lambda-based password rotation.

---

### Q6: Secrets in Kubernetes
**Question:** K8s Secrets are base64-encoded, not encrypted. How do you secure them?

**Expected Answer:**
- **EKS Envelope Encryption**: Enable KMS encryption for etcd secrets.
- **External Secrets Operator**: Store in AWS Secrets Manager; sync to K8s at runtime.
- **Sealed Secrets**: Encrypt client-side; only cluster can decrypt (Bitnami).
- **RBAC**: Restrict `get` on secrets to only needed service accounts.
- **Audit**: K8s audit logging tracks secret access.

---

## Section C: Network Security

### Q7: WAF & DDoS Protection
**Question:** How do you protect a public API on EKS from web attacks and DDoS?

**Expected Answer:**
- **AWS WAF**: Attach to ALB/API Gateway. Rules for SQLi, XSS, rate limiting.
- **Shield Standard**: Free L3/L4 DDoS protection (included with ALB).
- **CloudFront**: CDN absorbs volumetric attacks. Origin is the ALB.
- **Rate limiting**: WAF rate-based rules (e.g., 2000 req/5 min per IP).

---

### Q8: Security Scanning & Compliance
**Question:** How do you automate security scanning for containers and infrastructure?

**Expected Answer:**
- **Container images**: ECR scanning, Trivy in CI, Snyk for dependencies.
- **Infrastructure**: Checkov/tfsec for Terraform, AWS Config Rules for runtime.
- **Runtime**: Falco for K8s threat detection (unexpected processes, network).
- **GuardDuty**: EKS audit log monitoring, EC2 threat detection.
- **CIS Benchmarks**: `kube-bench` for EKS node validation.
