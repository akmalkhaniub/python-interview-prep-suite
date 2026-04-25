# Kubernetes & EKS Production Mastery

**Focus:** EKS, Deployments, Scaling, Production Troubleshooting

---

## Section A: EKS Architecture

### Q1: EKS Cluster Design
**Question:** Walk me through setting up a production EKS cluster from scratch.

**Expected Answer:**
When designing a production EKS cluster, a deeply secure foundation is paramount. I start by configuring the cluster with a Private API endpoint and placing all worker nodes strictly in private subnets, utilizing the AWS VPC CNI for native pod networking. I utilize Managed Node Groups to automate lifecycle management and patching, specifically separating critical cluster infrastructure (like CoreDNS and controllers) into dedicated system node groups, isolating them from volatile application workloads.

To aggressively optimize costs, the application node groups are configured with a mix of instance types to ensure high availability across Spot instance pools, strongly preferring ARM-based Graviton instances for superior price-to-performance ratios. I provision essential add-ons including the AWS Load Balancer Controller and the EBS CSI driver. For security, every pod requiring AWS access must be configured with IRSA (IAM Roles for Service Accounts), strictly avoiding node-level IAM profiles. Finally, enabling EKS Control Plane Logging to CloudWatch is mandatory for auditing and API server troubleshooting.

---

### Q2: EKS vs. Self-Managed K8s
**Question:** Why EKS instead of kubeadm on EC2?

**Expected Answer:**
The decision to use EKS over a self-managed Kubernetes installation like `kubeadm` on EC2 comes down to operational overhead versus control. EKS abstracts away the most complex and fragile components of Kubernetes: the control plane and the `etcd` database. AWS automatically handles high availability, backups, and the scaling of the API servers, freeing engineering teams to focus on delivering applications rather than managing infrastructure.

Furthermore, EKS provides deep, native integrations into the AWS ecosystem that are painful to replicate manually, such as seamless ALB Ingress, IRSA for granular pod-level AWS permissions, and immediate observability via CloudWatch Container Insights. EKS also drastically simplifies security by natively supporting OIDC federation and KMS envelope encryption for Kubernetes secrets. The primary trade-off is the $0.10/hour cluster fee and the loss of access to deeply customize the control plane configuration, which is an acceptable compromise for almost all enterprises prioritizing stability.

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
Horizontal Pod Autoscaler (HPA), Vertical Pod Autoscaler (VPA), and Karpenter solve different dimensions of scaling. HPA reacts to utilization metrics (like CPU or custom application metrics) by scaling the total number of pod replicas horizontally. Conversely, VPA analyzes historical usage and adjusts the actual CPU and memory requests/limits of existing pods vertically. It is critical to note that HPA and VPA generally cannot be used together on the exact same metric, as they will enter a race condition fighting over the pod's state.

Karpenter operates at the node level, completely replacing the legacy Cluster Autoscaler. When HPA scales up and demands more capacity than currently available, pods enter a `Pending` state. Karpenter instantly intercepts these pending pod requests, analyzes their specific CPU, memory, and architecture requirements, and directly provisions perfectly right-sized EC2 instances to accommodate them within seconds. For asynchronous workloads, I often pair HPA with KEDA to scale pods based on external metrics, like the depth of an SQS queue, driving a highly reactive, event-driven scaling pipeline.

---

### Q5: Resource Requests & Limits
**Question:** Should you always set both `requests` and `limits`?

**Expected Answer:**
Managing resource requests and limits requires a nuanced approach based on Kubernetes Quality of Service (QoS) classes. `Requests` must always be set; they are the contract between the pod and the `kube-scheduler` dictating exactly how much capacity must be available on a node for placement. I strongly mandate setting strict `Memory limits`, because memory is incompressible. If a pod exceeds its memory bounds without a limit, it risks starving the entire node, triggering a systemic Out-Of-Memory (OOM) kill cascade.

However, `CPU limits` are highly controversial. Because CPU is a compressible resource, exceeding a limit results in throttling, artificially degrading application performance rather than crashing the pod. Many high-performance engineering teams completely remove CPU limits, allowing pods to burst into idle node cycles. Based on these settings, Kubernetes assigns QoS classes: `Guaranteed` (requests equal limits), `Burstable`, or `BestEffort`. Under extreme node pressure, the kubelet will ruthlessly evict `BestEffort` pods first, highlighting why enforcing sensible defaults via namespace-level `LimitRange` configurations is critical.

---

## Section C: Cluster Upgrades & Maintenance

### Q6: EKS Upgrade Strategy
**Question:** How do you upgrade EKS from 1.28 to 1.30 with zero downtime?

**Expected Answer:**
Achieving a zero-downtime EKS upgrade requires a meticulous, phased approach, as Kubernetes control planes strictly cannot be downgraded once updated. The absolute first step is running analysis tools like `pluto` or `kubent` against your cluster and CI/CD pipelines to detect and remediate any usage of deprecated APIs that will be removed in the target versions.

Next, you must upgrade the EKS control plane sequentially, moving one minor version at a time (e.g., 1.28 to 1.29, then 1.29 to 1.30). Following the control plane update, the cluster's managed add-ons (VPC CNI, CoreDNS) must be updated to their compatible versions. Finally, you execute a rolling update of the worker nodes. The safest method is provisioning entirely new managed node groups running the 1.30 AMI, and gracefully cordoning and draining the old nodes. Throughout this process, properly configured Pod Disruption Budgets (PDBs) guarantee that critical services maintain minimum availability, ensuring end-users never experience an outage during the transition.

---

### Q7: Helm vs. Kustomize
**Question:** When do you choose Helm over Kustomize?

**Expected Answer:**
Helm and Kustomize represent two fundamentally different philosophies for managing Kubernetes manifests. Helm operates as a powerful package manager and templating engine. It heavily relies on Go-templates to dynamically generate YAML, making it the undeniable standard for packaging and distributing complex, third-party infrastructure components (like Prometheus or the AWS Load Balancer Controller) across the community.

Conversely, Kustomize is entirely patch-based and built directly into `kubectl`. It rejects templating in favor of maintaining raw, pure YAML bases that are mutated via strategic merge patches for different environments (e.g., dev, staging, prod). Kustomize is generally superior for managing internal, proprietary applications because it reduces cognitive load and avoids complex template logic. In a modern GitOps pipeline powered by ArgoCD, I utilize both simultaneously: Helm to deploy vendor infrastructure, and Kustomize overlays to manage our internal application configurations.

---

### Q8: Network Policies
**Question:** How do you implement zero-trust networking within EKS?

**Expected Answer:**
Implementing true zero-trust networking within an EKS cluster requires moving from a flat network architecture to strict micro-segmentation. I start by deploying a default-deny Network Policy in every namespace, immediately blocking all unapproved ingress and egress traffic. Developers must then define explicit rules mapping exactly which services are permitted to communicate. Crucially, while the default AWS VPC CNI routes traffic, it historically did not enforce Network Policies on its own; a secondary network agent like Calico, or a modern eBPF-based CNI like Cilium, must be installed to actually execute the blocking.

For deep, cryptographic zero-trust, Network Policies at OSI Layer 4 are insufficient. I would implement a Service Mesh like Istio or Linkerd to ensure all pod-to-pod communication is encrypted via mutual TLS (mTLS). This mesh also allows for the enforcement of granular Layer 7 AuthorizationPolicies, verifying not just the IP address, but the cryptographic identity of the calling service before permitting an API request. Finally, I strongly advocate restricting egress DNS to prevent data exfiltration from compromised pods.

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
Securing a multi-tenant EKS cluster relies on establishing rigid logical boundaries to prevent "noisy neighbor" scenarios and unauthorized access. The foundational boundary is the Kubernetes Namespace; every team or distinct project must operate within its own dedicated namespace. To prevent one team from consuming the entire cluster's compute capacity, strict `ResourceQuotas` must be applied to each namespace, establishing hard ceilings on total CPU and memory usage.

Security isolation is enforced via default-deny Network Policies that explicitly prevent cross-namespace communication unless intentionally whitelisted. From an access perspective, Role-Based Access Control (RBAC) must be tightly scoped; teams receive `RoleBindings` that grant administrative control exclusively within their designated namespace, preventing them from seeing or mutating resources cluster-wide. Finally, I configure distinct Priority Classes to ensure that mission-critical system infrastructure can aggressively preempt and evict lower-priority tenant workloads during periods of extreme resource contention.
