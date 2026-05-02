# 05 - DevOps, Containers & CI/CD

## Containers

### 1. What is the difference between a "Virtual Machine" and a "Container"?
**Answer:**
- **VM:** Virtualizes the hardware. Includes a full OS. Slower to start and heavier.
- **Container:** Virtualizes the OS. Shares the host kernel. Faster, lighter, and more portable.

### 2. Best practices for a "Lean" Docker image?
**Answer:**
- Use **Multi-stage builds**.
- Use small base images (e.g., Alpine).
- Minimize the number of layers.
- Don't include build dependencies in the final production image.

### 3. What is "Orchestration" (Kubernetes)?
**Answer:**
Managing the deployment, scaling, and networking of many containers. Key concepts: Pods, Services, Deployments, and Ingress.

## CI/CD

### 4. Explain the CI/CD pipeline stages for a Backend service.
**Answer:**
1. **Source:** Triggered by a Git push.
2. **Build:** Compile code and build Docker image.
3. **Test:** Run Unit, Integration, and Linting tests.
4. **Publish:** Push image to a Registry.
5. **Deploy:** Update the orchestrator (e.g., K8s) to use the new image.

### 5. What are "Blue-Green" and "Canary" deployments?
**Answer:**
- **Blue-Green:** Switch all traffic from the old version (Blue) to the new version (Green) at once.
- **Canary:** Gradually roll out the new version to a small percentage of users first to monitor for issues.

### 6. Role of "Infrastructure as Code" (Terraform)?
**Answer:**
Defining your cloud resources (VMs, DBs, Networks) in code files. Ensures environments are reproducible, versioned, and avoids manual configuration errors.
