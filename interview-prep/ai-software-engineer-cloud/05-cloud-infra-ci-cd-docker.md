# 05 - Infrastructure, CI/CD & Docker

## Containerization (Docker)

### 1. Best practices for "Production Dockerfiles"?
**Answer:**
- **Multi-stage builds:** To keep images small (only include runtime, not build tools).
- **Non-root user:** For security.
- **Layer Optimization:** Putting rarely changed steps (installing OS packages) at the top.
- **Specific Tags:** Avoid `latest`; use versioned tags for reproducibility.

### 2. What is "Container Orchestration" (Kubernetes)?
**Answer:**
Managing the deployment, scaling, and networking of many containers. Essential for high-scale AI backends.

### 3. Difference between a "Sidecar" and an "Adapter" pattern?
**Answer:**
- **Sidecar:** A container that runs alongside the main app to provide helper functions (e.g., logging, proxying).
- **Adapter:** A sidecar that standardizes the interface of the main app (e.g., converting log format).

## DevOps (CI/CD)

### 4. What is GitOps (Terraform + Git)?
**Answer:**
A practice where Git is the "Source of Truth" for infrastructure. Changes to the infra are made via PRs, and tools like Atlantis or Terraform Cloud apply them automatically.

### 5. How do you integrate ML testing into a CI pipeline?
**Answer:**
- **Unit Tests:** For preprocessing logic.
- **Property-based Tests:** For data validation.
- **Integration Tests:** For model serving APIs.
- **Evaluation Tests:** Checking model metrics (accuracy, loss) against a baseline.

### 6. Importance of "Vulnerability Scanning" (Trivy, Snyk)?
**Answer:**
Automatically checking Docker images and dependencies for known security flaws during the CI process.
