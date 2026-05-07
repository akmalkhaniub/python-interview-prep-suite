# Deep Dive: Kubernetes (EKS) in Production

## 🏗️ EKS Architecture
*   **Control Plane:** Managed by AWS.
*   **Data Plane:** 
    *   **Managed Node Groups:** AWS handles EC2 lifecycle.
    *   **Fargate:** Serverless containers (good for intermittent jobs, not for heavy ClickHouse/RDS types).
    *   **Self-Managed Nodes:** Maximum control (used for custom AMIs built with Packer).

## 🚀 Scaling & Availability
*   **Cluster Autoscaler:** Adjusts the number of nodes based on pending pods.
*   **Karpenter:** A modern, faster alternative to Cluster Autoscaler that provisions right-sized nodes instantly.
*   **HPA (Horizontal Pod Autoscaler):** Scales pods based on CPU/RAM metrics.
*   **VPA (Vertical Pod Autoscaler):** Suggests or applies changes to container resource requests.

## 🕸️ Networking & Ingress
*   **AWS Load Balancer Controller:** Manages ALBs/NLBs automatically via K8s Ingress/Service objects.
*   **VPC CNI:** Assigns real VPC IP addresses to pods for better performance and security group integration.
*   **Calico/Cilium:** For Network Policies (Pod-to-Pod traffic control).

## 💡 Interview Q&A
**Q: How do you secure pod-to-pod communication in EKS?**
**A:** Use **Network Policies** (requires a CNI like Calico) to whitelist traffic. Also, use **IRSA (IAM Roles for Service Accounts)** so pods can only access the AWS resources (S3, RDS) they specifically need.

**Q: A pod is in `Pending` state. How do you troubleshoot?**
**A:** Run `kubectl describe pod <name>`. Common reasons:
1.  Insufficient CPU/Memory in the cluster.
2.  Taints/Tolerations mismatch.
3.  PVC (Persistent Volume Claim) binding failure.
4.  Node Selector constraints.

## 🛠️ Code Snippet: IRSA Policy Example
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: s3-reader
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/S3ReadRole
```
