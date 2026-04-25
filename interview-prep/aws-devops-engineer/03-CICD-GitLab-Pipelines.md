# CI/CD & GitLab Pipelines

**Focus:** GitLab CI/CD, Multi-Layer Pipelines, Deployment Strategies

---

## Section A: GitLab CI Fundamentals

### Q1: Pipeline Architecture
**Question:** Design a GitLab CI pipeline for a monorepo with backend, frontend, and Terraform. How do you handle selective builds?

**Expected Answer:**
Designing a robust pipeline for a monorepo requires isolating component builds to avoid unnecessary execution. I would use the `rules:changes` keyword to ensure that frontend changes only trigger frontend jobs, and backend changes only trigger backend jobs. For pipeline architecture, I advocate for parent-child pipelines using the `trigger:include` pattern, allowing each major component to maintain its own decoupled `.gitlab-ci.yml` file.

The overarching execution flow should rigidly follow standard stages: `lint`, `test`, `build`, `infra` (Terraform), `deploy-staging`, and finally `deploy-prod`. To significantly reduce execution time, I would aggressively implement caching. By configuring branch-specific cache keys, dependencies like `node_modules`, Python `pip` packages, and `.terraform` plugin directories are preserved between runs, eliminating redundant downloads and speeding up feedback loops for developers.

```yaml
backend-test:
  stage: test
  script: pytest
  rules:
    - changes:
        - backend/**/*
```

---

### Q2: Self-Hosted Runners
**Question:** When would you use self-hosted GitLab Runners on AWS?

**Expected Answer:**
Self-hosted GitLab Runners are necessary when you have strict networking, security, or performance requirements that SaaS runners cannot meet. Specifically, I deploy self-hosted runners when pipelines require direct access to private resources within an AWS VPC (like internal databases), when strict data sovereignty and compliance mandates exist, or when specialized hardware like GPUs is needed for ML builds. At high volumes, self-hosting also becomes significantly cheaper.

To manage scale, I deploy the GitLab Runner using the Kubernetes executor within an EKS cluster, allowing CI jobs to spin up dynamically as ephemeral pods and scale back to zero when idle. Because these ephemeral pods lose local state, configuring an Amazon S3 bucket as a distributed cache backend is mandatory to share state across jobs. From a security standpoint, the runners must reside in a strictly isolated CI/CD AWS account and use IAM assume-role patterns to securely access destination accounts, ensuring the CI environment itself is never implicitly trusted.

---

### Q3: Secrets in CI/CD
**Question:** How do you securely inject AWS credentials into a GitLab pipeline?

**Expected Answer:**
The modern gold standard for securely injecting AWS credentials into any CI/CD pipeline is OpenID Connect (OIDC) federation. I would configure GitLab as an OIDC Identity Provider within AWS IAM. This allows the GitLab pipeline to exchange a short-lived JSON Web Token (JWT) for temporary AWS credentials using `AssumeRoleWithWebIdentity`, completely eliminating the need to store long-lived, static access keys that could be leaked or compromised.

If OIDC cannot be used, or for non-AWS secrets, I would integrate HashiCorp Vault using GitLab JWT authentication for dynamic secret generation. As a last resort, native GitLab CI/CD variables can be used, but they must strictly be configured as both "Masked" (preventing output in logs) and "Protected" (limiting access to protected branches). It is an absolute, non-negotiable rule that secrets must never be hardcoded into `.gitlab-ci.yml` files, Dockerfiles, or any application source code.

---

## Section B: Deployment Strategies

### Q4: Blue/Green vs. Canary vs. Rolling
**Question:** Explain the differences. When would you use each for an EKS-hosted service?

**Expected Answer:**
Understanding the trade-offs of deployment strategies is key to platform stability. A Rolling Deployment is the Kubernetes default; it gradually replaces old pods with new ones. While resource-efficient, it means both versions serve traffic simultaneously during the rollout, which can cause issues with strict API contracts. Blue/Green deployments provision two entirely separate, full-scale environments. Traffic is instantly switched at the load balancer or DNS level, allowing for instant rollback, but this requires provisioning 2x the compute resources during the transition.

A Canary deployment sits between the two, routing a small percentage of traffic (e.g., 5%) to the new version while monitoring error rates, and gradually increasing the split. This limits the blast radius of a bad release but requires advanced traffic management capabilities. For modern EKS workloads, I strongly recommend implementing Argo Rollouts. It natively integrates with service meshes and ingress controllers to handle complex Canary or Blue/Green traffic splitting, and it can automatically halt and rollback a release if Prometheus metrics indicate elevated error rates.

---

### Q5: Database Migrations in CI/CD
**Question:** How do you run PostgreSQL schema migrations safely in a pipeline?

**Expected Answer:**
Running database migrations in a CI/CD pipeline without causing downtime requires strict adherence to the Expand-Contract pattern. Using tools like Alembic or Flyway, the migration must run as a distinct pipeline stage *before* the application deployment. To prevent locking production tables, DDL operations must be carefully crafted: for PostgreSQL, this means using `CREATE INDEX CONCURRENTLY` and adding columns as nullable, which are virtually instant operations.

The Expand-Contract pattern dictates that breaking changes occur over multiple deployments. First, you expand the schema by adding a new nullable column. Next, you deploy the application to dual-write to both columns and run a background task to backfill old data. Finally, a subsequent deployment switches the application to read exclusively from the new column, after which the old column can safely be dropped (the contract phase). Furthermore, every single migration script must be paired with a rigorously tested `down` rollback script to ensure the pipeline can safely revert a failed schema change.

---

### Q6: Infrastructure Pipeline
**Question:** How do you safely run `terraform apply` in CI/CD?

**Expected Answer:**
Executing Terraform in a CI/CD pipeline safely requires separating the planning and applying phases. The pipeline should run `terraform plan` automatically on every Merge Request, posting the output directly as a comment for peer review alongside automated security and cost checks from tools like Checkov and Infracost. Critically, the pipeline must export the exact binary plan file as an artifact.

When the code is merged, the `terraform apply` stage must consume that exact saved plan artifact to guarantee that what was reviewed is exactly what gets provisioned. For development environments, non-destructive plans can be configured to auto-apply to accelerate feedback loops. However, for production environments, the apply stage must always be gated with `when: manual` and restricted to protected branches, requiring explicit human approval before any infrastructure state is mutated.

---

## Section C: Advanced Patterns

### Q7: Multi-Layer Pipeline Orchestration
**Question:** Deploy in order: Terraform → DB migrations → Backend → Frontend. How?

```yaml
stages:
  - infra
  - migrate
  - deploy-backend
  - deploy-frontend
  - smoke-test

terraform-apply:
  stage: infra
  when: manual

db-migrate:
  stage: migrate
  needs: [terraform-apply]  # DAG dependency

deploy-api:
  stage: deploy-backend
  needs: [db-migrate]

deploy-web:
  stage: deploy-frontend
  needs: [deploy-api]

smoke:
  stage: smoke-test
  needs: [deploy-web]
  script: curl -f https://app.example.com/healthz
```

---

### Q8: Docker Image Build & Registry
**Question:** How do you build and push images efficiently in GitLab CI?

**Expected Answer:**
Building efficient Docker images in CI begins with strict multi-stage Dockerfiles, separating the heavy build dependencies from the lightweight runtime environment to drastically reduce the final image size and attack surface. To accelerate the build phase, I always utilize Docker layer caching by pulling the previous image and using the `--cache-from` flag, which ensures unchanged layers do not recompile.

Once built, the image must be pushed to a secure registry like AWS ECR after authenticating via `aws ecr get-login-password`. The absolute most critical rule is image tagging: images must be tagged with an immutable identifier, such as the Git commit hash (`$CI_COMMIT_SHA`). Using mutable tags like `latest` in production Kubernetes manifests is a severe anti-pattern that breaks reproducibility and rollbacks. Finally, before the deployment stage can proceed, the image must be scanned for CVEs using a tool like Trivy or native ECR scanning to block vulnerable containers from reaching production.

---

### Q9: Pipeline Reliability
**Question:** Pipeline fails intermittently due to flaky tests. How do you handle it?

**Expected Answer:**
Flaky tests are the enemy of CI/CD trust and velocity. When a test becomes intermittent, my immediate action is to quarantine it by moving it out of the critical path; it should still run to gather telemetry, but its failure must not block the main deployment pipeline. The root cause of flakiness almost always traces back to shared database state, asynchronous timing issues, or unmocked external network dependencies.

To permanently fix these issues, I advocate for utilizing Testcontainers to spin up ephemeral, highly isolated database instances per test suite to eliminate state bleeding. I also strictly mandate the mocking of all external API boundaries using tools like WireMock. While it is acceptable to use the GitLab `retry` keyword for inherent infrastructure flakiness (like transient network timeouts or package registry 503 errors), it should never be used as a band-aid to mask fundamentally poorly written, race-condition-prone test logic.
