# Kubernetes & EKS Production Mastery

**Focus:** EKS, Deployments, Scaling, Production Troubleshooting

---

## Section A: EKS Architecture

### Q1: EKS Cluster Design
**Question:** Walk me through setting up a production EKS cluster from scratch.

**Expected Answer:**
- **Networking**: Private API endpoint, private subnets for nodes, VPC CNI for pod networking.
- **Node Groups**: Managed node groups (easier patching) vs. self-managed (more control). Separate system vs. app node groups.
- **Instance types**: Mixed types in node group for Spot diversity. Graviton for cost.
- **Add-ons**: CoreDNS, kube-proxy, VPC CNI, AWS LB Controller, EBS CSI driver.
- **IRSA**: IAM Roles for Service Accounts for all workloads needing AWS access.
- **Logging**: Control plane logs to CloudWatch (API server, audit, authenticator).

---

### Q2: EKS vs. Self-Managed K8s
**Question:** Why EKS instead of kubeadm on EC2?

**Expected Answer:**
- **Managed control plane**: AWS handles etcd, API server HA, upgrades.
- **Integrations**: Native ALB Ingress, IRSA, CloudWatch Container Insights.
- **Security**: OIDC, envelope encryption for secrets with KMS.
- **Trade-off**: Less control over control plane; $0.10/hr cost.

---

## Section B: Deployments & Scaling

### Q3: Deployment Troubleshooting
**Question:** A deployment is stuck in `Pending`. Walk me through debugging.

**Expected Answer:**
```bash
# Step 1: Check pod status
kubectl describe pod <pod-name> -n <namespace>

# Common causes:
# - Insufficient resources
# - Node selector/affinity mismatch
# - PVC not bound
# - Image pull errors (wrong tag, ECR auth)
# - Taints without tolerations

# Step 2: Check events
kubectl get events --sort-by=.metadata.creationTimestamp -n <namespace>

# Step 3: Check node capacity
kubectl describe nodes | grep -A5 'Allocated resources'
```

---

### Q4: HPA vs. VPA vs. Karpenter
**Question:** Explain the difference. Can they work together?

**Expected Answer:**
- **HPA**: Scales pod count based on CPU/memory/custom metrics.
- **VPA**: Adjusts pod resource requests/limits. Cannot run with HPA on same metric.
- **Karpenter**: Node-level autoscaler. Right-sized nodes for pending pods.
- **Together**: HPA scales pods → pods go `Pending` → Karpenter provisions nodes.
- **Custom metrics HPA**: Scale on SQS queue depth via KEDA or Prometheus Adapter.

---

### Q5: Resource Requests & Limits
**Question:** Should you always set both `requests` and `limits`?

**Expected Answer:**
- **Requests**: Used by scheduler for placement. Always set.
- **Memory limits**: Set to prevent OOMKill of other pods.
- **CPU limits**: Controversial. CPU is compressible — pods get throttled, not killed. Many teams remove CPU limits.
- **QoS Classes**: `Guaranteed` (requests == limits), `Burstable`, `BestEffort`. Eviction order: BestEffort first.
- **LimitRange**: Enforce defaults at namespace level.

---

## Section C: Cluster Upgrades & Maintenance

### Q6: EKS Upgrade Strategy
**Question:** How do you upgrade EKS from 1.28 to 1.30 with zero downtime?

**Expected Answer:**
- **Step 1**: Check deprecated APIs with `pluto` or `kubent`. Fix manifests.
- **Step 2**: Upgrade control plane (1.28 → 1.29 → 1.30). One minor version at a time.
- **Step 3**: Upgrade managed add-ons to compatible versions.
- **Step 4**: Rolling upgrade node groups. Create new node group with 1.30 AMI, drain old.
- **PDBs**: Ensure PodDisruptionBudgets allow at most 1 unavailable.
- **Rollback**: You CANNOT downgrade the control plane. Test in staging.

---

### Q7: Helm vs. Kustomize
**Question:** When do you choose Helm over Kustomize?

**Expected Answer:**
- **Helm**: Templating, package management, rollback. Best for third-party charts.
- **Kustomize**: Patch-based, no templating, native `kubectl`. Best for internal apps with env overlays.
- **Together**: Helm for community charts, Kustomize for your apps.
- **GitOps**: ArgoCD supports both.

---

### Q8: Network Policies
**Question:** How do you implement zero-trust networking within EKS?

**Expected Answer:**
- **Network Policies**: Default-deny all ingress/egress per namespace. Explicitly allow needed flows.
- **CNI requirement**: VPC CNI alone doesn't enforce policies. Need Calico or Cilium.
- **Service Mesh**: Istio/Linkerd for mTLS, fine-grained AuthorizationPolicies.
- **DNS policies**: Restrict egress DNS for sensitive workloads.

---

## Section D: Production Scenarios

### Q9: CrashLoopBackOff Deep Dive
**Question:** A pod is in `CrashLoopBackOff` and dies instantly. How do you debug?

**Expected Answer:**
```bash
# Logs from last crash
kubectl logs <pod> --previous

# Check for OOMKilled
kubectl describe pod <pod> | grep -i oom

# Override entrypoint to keep alive
kubectl debug <pod> -it --copy-to=debug-pod --container=app -- /bin/sh

# Common causes: missing env vars, DB connection refused, OOMKilled, probe failure
```

---

### Q10: Multi-Tenant Cluster
**Question:** Multiple teams share one EKS cluster. How do you ensure isolation?

**Expected Answer:**
- **Namespaces**: One per team/service.
- **ResourceQuotas**: Limit CPU/memory per namespace.
- **Network Policies**: Namespace-to-namespace isolation.
- **RBAC**: Team-scoped ClusterRoles bound to their namespace.
- **Priority Classes**: Ensure critical workloads aren't preempted.
