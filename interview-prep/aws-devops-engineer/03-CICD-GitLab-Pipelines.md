# CI/CD & GitLab Pipelines

**Focus:** GitLab CI/CD, Multi-Layer Pipelines, Deployment Strategies

---

## Section A: GitLab CI Fundamentals

### Q1: Pipeline Architecture
**Question:** Design a GitLab CI pipeline for a monorepo with backend, frontend, and Terraform. How do you handle selective builds?

**Expected Answer:**
- **`rules:changes`** to trigger only when specific paths change.
- **Stages**: `lint` → `test` → `build` → `infra` → `deploy-staging` → `deploy-prod`.
- **Parent-child pipelines**: `trigger:include` for component-specific `.gitlab-ci.yml`.
- **Caching**: `cache:key` per branch for `node_modules`, `pip`, `.terraform`.

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
- **When**: VPC access needed, compliance, GPU builds, cost optimization at scale.
- **Scaling**: GitLab Runner with Kubernetes executor — jobs as K8s pods.
- **Caching**: S3 as distributed cache backend.
- **Security**: Runners in dedicated CI/CD account, assume roles cross-account.

---

### Q3: Secrets in CI/CD
**Question:** How do you securely inject AWS credentials into a GitLab pipeline?

**Expected Answer:**
- **OIDC Federation** (preferred): GitLab OIDC → AWS IAM Identity Provider → `AssumeRoleWithWebIdentity`. No long-lived credentials.
- **GitLab CI/CD Variables**: Masked + Protected for fallback.
- **Vault integration**: HashiCorp Vault with GitLab JWT auth.
- **Never**: Hardcode in `.gitlab-ci.yml` or Dockerfiles.

---

## Section B: Deployment Strategies

### Q4: Blue/Green vs. Canary vs. Rolling
**Question:** Explain the differences. When would you use each for an EKS-hosted service?

**Expected Answer:**
- **Rolling**: Default K8s. Gradually replaces old pods. Both versions serve traffic during rollout.
- **Blue/Green**: Two full environments; switch traffic at LB/DNS. Instant rollback. 2x resources.
- **Canary**: Route small % to new version, monitor, gradually increase. Needs traffic splitting.
- **EKS choice**: Argo Rollouts for Canary/Blue-Green with automatic analysis.

---

### Q5: Database Migrations in CI/CD
**Question:** How do you run PostgreSQL schema migrations safely in a pipeline?

**Expected Answer:**
- **Tool**: Alembic (Python), Flyway, or Sqitch.
- **Expand-Contract**: Add new column (nullable) → deploy dual-write → backfill → switch reads → drop old.
- **Non-locking DDL**: `CREATE INDEX CONCURRENTLY`, nullable `ADD COLUMN` is instant in PG 11+.
- **Pipeline**: Migration runs BEFORE app deployment.
- **Rollback**: Every migration has a `down` script. Test in staging.

---

### Q6: Infrastructure Pipeline
**Question:** How do you safely run `terraform apply` in CI/CD?

**Expected Answer:**
- **MR workflow**: `plan` on MR, output as comment. `apply` requires `when: manual` + protected environments.
- **Plan artifact**: Save plan file; apply uses the SAME plan.
- **Auto-apply for dev**: Non-destructive changes can auto-apply. Prod always requires gate.
- **Policy checks**: `checkov` and `infracost` in pipeline.

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
- **Multi-stage Dockerfiles**: Separate build and runtime stages.
- **Layer caching**: `docker build --cache-from` previous image.
- **ECR**: Authenticate with `aws ecr get-login-password`.
- **Tagging**: `$CI_COMMIT_SHA` (immutable). Never use `latest` in prod K8s manifests.
- **Scanning**: `trivy` or ECR native scanning for CVEs.

---

### Q9: Pipeline Reliability
**Question:** Pipeline fails intermittently due to flaky tests. How do you handle it?

**Expected Answer:**
- **Quarantine**: Tag flaky tests, run separately, don't block main pipeline.
- **Root cause**: Usually shared state, timing, or external dependencies.
- **Fixes**: Testcontainers for isolated DB, mock external APIs, `wait-for-it` scripts.
- **`retry`**: OK for infra flakiness (network timeouts), NOT for test logic.
