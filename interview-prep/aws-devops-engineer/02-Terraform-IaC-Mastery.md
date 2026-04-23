# Terraform & Infrastructure as Code Mastery

**Focus:** Terraform, Modular IaC, State Management

---

## Section A: Terraform Fundamentals & State

### Q1: State Management Strategy
**Question:** How do you manage Terraform state in a team of 5 engineers all deploying to the same AWS account?

**Expected Answer:**
- **Remote backend**: S3 bucket + DynamoDB table for state locking.
- **Locking**: DynamoDB provides a lease-based lock. Second engineer gets `Error: Error locking state`.
- **State per environment**: Separate state files via workspaces OR separate backend configs.
- **Never commit state**: `.gitignore` the `.tfstate` files; they contain secrets.
- **State encryption**: S3 server-side encryption (SSE-S3 or SSE-KMS).
- **Terraform Cloud** alternative: Built-in locking, run queuing, policy-as-code.

---

### Q2: Workspaces vs. Directory Structure
**Question:** Should you use Terraform workspaces to manage dev/staging/prod, or separate directories?

**Expected Answer:**
- **Workspaces**: Same code, different state. Risk: forgetting which workspace you're in.
- **Directory structure**: `envs/dev/`, `envs/staging/`, `envs/prod/` each calling shared modules. More explicit.
- **Best practice for production**: Directory-per-env with shared modules.
- **Terragrunt**: Alternative that DRYs up the directory approach with `terragrunt.hcl` inheritance.

---

### Q3: Drift Detection
**Question:** A developer manually changed a Security Group rule in the AWS Console. How do you detect and remediate?

**Expected Answer:**
- **Scheduled `terraform plan`** in CI (nightly GitLab scheduled pipeline) — alerts on any diff.
- **AWS Config Rules**: Detect non-compliant resources.
- **Remediation**: (a) `terraform apply` to revert, (b) `terraform import` if the change is desired.
- **Prevention**: Restrict console access via IAM; tag TF-managed resources.

---

## Section B: Modules & Reusability

### Q4: Module Design Philosophy
**Question:** How do you design a Terraform module that is reusable across teams but not overly complex?

**Expected Answer:**
- **Single responsibility**: One module = one concern (e.g., `module/vpc`, `module/eks-cluster`).
- **Sensible defaults**: Required vars should be minimal; use `variable` defaults for 80% case.
- **Output everything useful**: Other modules will need IDs, ARNs, endpoints.
- **Version pinning**: Publish to private registry or Git tags; consumers pin `source = "git::...?ref=v2.1.0"`.
- **Anti-pattern**: God modules that create VPC + EKS + RDS + IAM in one.

---

### Q5: Handling Secrets in Terraform
**Question:** How do you manage sensitive values in Terraform without exposing them?

**Expected Answer:**
- **`sensitive = true`** on variables/outputs — hides from CLI but NOT from state.
- **State is still sensitive**: Encrypt the S3 backend; restrict access.
- **Generate secrets externally**: Use `aws_secretsmanager_secret_version` data source to read, not create.
- **Random passwords**: `random_password` resource + store in Secrets Manager.
- **Never use `default` for secrets**: Force injection via env vars or `.tfvars` (gitignored).

---

### Q6: Terraform Testing
**Question:** How do you test Terraform code before it hits production?

**Expected Answer:**
- **Static analysis**: `tflint`, `checkov`/`tfsec`, `terraform validate`.
- **Plan review**: `terraform plan` output in GitLab MR as a comment.
- **Infracost**: Cost estimation on PRs.
- **Integration tests**: Terratest (Go) — spins up real infra, validates, tears down.
- **Policy-as-Code**: OPA/Rego or Sentinel for governance.
- **Ephemeral environments**: Apply to a throwaway account, run smoke tests, destroy.

---

## Section C: Advanced Terraform Patterns

### Q7: Refactoring Without Destruction
**Question:** You need to move a resource from one module to another without recreating it. How?

**Expected Answer:**
```hcl
# Terraform 1.1+ moved blocks (preferred)
moved {
  from = module.old_module.aws_rds_cluster.main
  to   = module.new_module.aws_rds_cluster.main
}
```
- **Alternative**: `terraform state mv` (manual, risky, not in code).
- **Always**: Take a state backup first (`terraform state pull > backup.tfstate`).
- **Verify**: Run `plan` — should show 0 changes if done correctly.

---

### Q8: `for_each` vs. `count`
**Question:** When do you prefer `for_each` over `count`?

**Expected Answer:**
- **`count`**: Index-based. Removing item at index 2 from a list of 5 causes items 3–4 to get recreated.
- **`for_each`**: Key-based. Removing one key doesn't affect others.
- **Rule**: Use `for_each` for resources that shouldn't be recreated. Use `count` for simple on/off toggles.

---

### Q9: Managing Multiple Providers
**Question:** How do you deploy resources across multiple AWS regions in a single Terraform configuration?

**Expected Answer:**
```hcl
provider "aws" {
  region = "us-east-1"
  alias  = "us_east"
}

provider "aws" {
  region = "eu-west-1"
  alias  = "eu_west"
}

module "eu_vpc" {
  source = "./modules/vpc"
  providers = {
    aws = aws.eu_west
  }
}
```
- **Cross-account**: Use `assume_role` block in the provider.
- **Caution**: Prefer separate state files per region for blast radius.

---

## Section D: Scenario Challenge

### Challenge: Import & Manage an Existing Resource
**Scenario:** A manually created S3 bucket `prod-data-lake` needs Terraform management.

```bash
# 1. Write the resource block matching existing config
# 2. Import into state
terraform import aws_s3_bucket.data_lake prod-data-lake

# 3. Verify zero diff
terraform plan

# 4. Terraform 1.5+ import blocks:
```

```hcl
import {
  to = aws_s3_bucket.data_lake
  id = "prod-data-lake"
}
# Then: terraform plan -generate-config-out=generated.tf
```

**Follow-up:** Risks of importing? State now tracks it — accidental `destroy` could delete production data. Use `lifecycle { prevent_destroy = true }`.
