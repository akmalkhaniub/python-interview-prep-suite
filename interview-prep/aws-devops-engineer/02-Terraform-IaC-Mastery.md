# Terraform & Infrastructure as Code Mastery

**Focus:** Terraform, Modular IaC, State Management

---

## Section A: Terraform Fundamentals & State

### Q1: State Management Strategy
**Question:** How do you manage Terraform state in a team of 5 engineers all deploying to the same AWS account?

**Expected Answer:**
To safely manage Terraform state in a collaborative environment, the foundation is establishing a remote backend. I would configure an Amazon S3 bucket to securely store the state file, paired with a DynamoDB table to handle state locking. The DynamoDB lock is critical; if two engineers run `terraform apply` concurrently, the second engineer will receive an `Error locking state` message, preventing state corruption. 

Furthermore, I strictly isolate state by environment, either by utilizing Terraform workspaces or, preferably, by using completely separate backend configurations for dev, staging, and production. It is an absolute rule never to commit `.tfstate` files to version control by ensuring they are added to `.gitignore`, as they inherently contain sensitive information in plain text. To protect this data at rest, I mandate server-side encryption (SSE-KMS) on the S3 bucket. Alternatively, utilizing Terraform Cloud or Enterprise is an excellent managed option that natively handles state locking, run queuing, and policy-as-code guardrails without maintaining the underlying S3/DynamoDB infrastructure.

---

### Q2: Workspaces vs. Directory Structure
**Question:** Should you use Terraform workspaces to manage dev/staging/prod, or separate directories?

**Expected Answer:**
While Terraform workspaces allow you to use the exact same directory of code with different state files, they introduce significant human error risk because it's very easy to accidentally apply production changes when you think you are in the development workspace. Therefore, the industry best practice for production infrastructure is to use a strict directory-based structure, such as `envs/dev/`, `envs/staging/`, and `envs/prod/`. 

This explicit directory isolation ensures that engineers know exactly which environment they are targeting. In this architecture, each environment directory simply contains variable definitions and backend configurations that invoke versioned, shared Terraform modules. To further enhance this pattern and keep the codebase DRY (Don't Repeat Yourself), I highly recommend using a wrapper tool like Terragrunt. Terragrunt allows you to maintain this explicit directory structure while using `terragrunt.hcl` files to inherit remote backend configurations and provider setups, eliminating boilerplate code.

---

### Q3: Drift Detection
**Question:** A developer manually changed a Security Group rule in the AWS Console. How do you detect and remediate?

**Expected Answer:**
Detecting configuration drift requires automated, proactive monitoring. The most effective method is to set up a scheduled CI/CD pipeline (such as a nightly GitLab schedule) that runs `terraform plan` against the production environments. If the plan outputs anything other than zero changes, the pipeline fails and sends an immediate alert to the engineering team. Additionally, implementing AWS Config Rules can provide real-time alerts if resources deviate from expected, compliant configurations.

When drift is detected, remediation takes one of two paths. If the manual change was unauthorized or accidental, running a standard `terraform apply` will automatically revert the infrastructure back to the defined state. If the manual change was an emergency hotfix that must be kept, the engineer must backport the change into the Terraform code and optionally use `terraform import` or state manipulation to reconcile the state. To prevent drift in the first place, I strictly enforce the principle of least privilege by stripping write permissions from AWS Console user roles, and I tag all resources managed by Terraform to clearly indicate they should not be manually modified.

---

## Section B: Modules & Reusability

### Q4: Module Design Philosophy
**Question:** How do you design a Terraform module that is reusable across teams but not overly complex?

**Expected Answer:**
A well-designed Terraform module adheres strictly to the single responsibility principle. Rather than creating sprawling "God modules" that attempt to build an entire VPC, EKS cluster, and RDS database all at once—which become impossible to maintain—I design modules to handle one specific architectural component, like `module/vpc` or `module/eks-cluster`. 

To maximize developer experience and reusability, modules must have sensible default values. I minimize the number of strictly required variables so that engineers can instantiate the module for the standard 80% use case with minimal configuration, while still allowing complex overrides when necessary. Furthermore, the module should actively export all useful attributes, such as resource IDs, ARNs, and connection endpoints, via the `outputs.tf` file so upstream modules can seamlessly consume them. Finally, strict versioning is non-negotiable. Modules should be published to a private Terraform registry or securely referenced via Git tags (e.g., `ref=v2.1.0`), ensuring that upstream updates do not unexpectedly break downstream consumer infrastructure.

---

### Q5: Handling Secrets in Terraform
**Question:** How do you manage sensitive values in Terraform without exposing them?

**Expected Answer:**
Managing secrets in Terraform requires a multi-layered approach because Terraform inherently stores all data, including secrets, in plain text within its `.tfstate` file. While setting `sensitive = true` on variables and outputs prevents secrets from being leaked into the CLI logs during a run, it does not encrypt them in the state file. Therefore, strictly locking down the S3 state bucket with IAM policies and enforcing SSE-KMS encryption is paramount.

When provisioning infrastructure that requires passwords, such as an RDS database, I utilize the `random_password` provider to dynamically generate the secret during execution. I immediately write this password into an AWS Secrets Manager or Parameter Store resource. For applications that need to consume existing secrets, I use the `aws_secretsmanager_secret_version` data source to read them at runtime rather than hardcoding them. Crucially, I never place secrets in `default` blocks inside variable files; any required external secrets must be injected securely via environment variables (e.g., `TF_VAR_db_password`) in an ephemeral CI/CD pipeline, ensuring they never touch version control.

---

### Q6: Terraform Testing
**Question:** How do you test Terraform code before it hits production?

**Expected Answer:**
A robust testing pipeline for infrastructure as code begins with the "shift-left" philosophy. On every commit, I execute rapid static analysis tools like `terraform validate` for syntax, `tflint` for cloud-provider-specific best practices, and `checkov` or `tfsec` to catch security misconfigurations before deployment. When a Pull Request is opened, the CI/CD pipeline automatically runs a `terraform plan` and injects the output directly into the PR comments for human review, alongside an automated cost estimate generated by Infracost.

To ensure strict compliance, I enforce Policy-as-Code utilizing Open Policy Agent (OPA) or HashiCorp Sentinel, automatically blocking deployments that violate organizational rules. For complex, mission-critical modules, I rely on integration testing using tools like Terratest (written in Go). Terratest programmatically applies the module to an ephemeral sandbox AWS account, executes assertions against the live infrastructure to verify functionality, and reliably destroys the resources afterward, guaranteeing the module works as designed in reality, not just in theory.

---

## Section C: Advanced Terraform Patterns

### Q7: Refactoring Without Destruction
**Question:** You need to move a resource from one module to another without recreating it. How?

**Expected Answer:**
Refactoring infrastructure safely, such as moving a resource into a new module without forcing a destructive recreation, has become significantly safer since Terraform 1.1 with the introduction of configuration-driven move blocks. By declaring a `moved { from = module.old_module.aws_rds_cluster.main; to = module.new_module.aws_rds_cluster.main }` block directly in the HCL code, Terraform understands the logical shift and updates the state mapping during the next apply without destroying the underlying AWS resource.

Prior to `moved` blocks, the only alternative was manually running CLI commands like `terraform state mv`, which is highly risky because it bypasses version control and leaves no audit trail. Regardless of the method used, the golden rule of state refactoring is to always pull a local backup of the state file (`terraform state pull > backup.tfstate`) before making any changes. Finally, before committing the code, running a `terraform plan` must definitively show exactly 0 resources to add, change, or destroy, confirming that the logical move was successful and no physical infrastructure will be touched.

---

### Q8: `for_each` vs. `count`
**Question:** When do you prefer `for_each` over `count`?

**Expected Answer:**
While both `count` and `for_each` allow you to create multiple instances of a resource, their underlying mechanisms behave fundamentally differently during updates. The `count` meta-argument relies on a numerical index (e.g., 0, 1, 2). If you have a list of five subnets and you remove the subnet at index 2, Terraform shifts all subsequent items down by one. This causes Terraform to mistakenly believe the resources at indexes 3 and 4 have changed, potentially forcing a destructive recreation of perfectly healthy infrastructure.

In contrast, `for_each` relies on unique string keys (usually via a map or a set of strings). If you remove a key from the map, Terraform perfectly identifies the specific resource mapped to that key and deletes only it, leaving the remaining resources entirely untouched. As a strict rule, `for_each` should always be used when deploying multiple similar resources, such as subnets or EC2 instances, to prevent catastrophic accidental deletions. I reserve `count` exclusively for simple boolean toggles, such as `count = var.create_resource ? 1 : 0`, to conditionally enable or disable a single resource.

---

### Q9: Managing Multiple Providers
**Question:** How do you deploy resources across multiple AWS regions in a single Terraform configuration?

**Expected Answer:**
To manage resources across multiple AWS regions or accounts within a single Terraform configuration, you must define multiple provider blocks and differentiate them using the `alias` meta-argument. For example, you can have a default AWS provider for `us-east-1` and a secondary provider explicitly aliased as `eu_west` pointing to `eu-west-1`. When instantiating a module or resource that needs to be deployed in the European region, you pass the specific provider alias using the `providers = { aws = aws.eu_west }` mapping block.

This same aliasing technique is utilized for cross-account deployments by embedding an `assume_role` block inside the aliased provider configuration, allowing Terraform to securely switch into a target account's role. While this approach is technically valid and useful for closely coupled resources (like establishing a cross-region VPC peering connection), as a general architectural best practice, I strongly caution against deploying entire environments across multiple regions or accounts from a single state file. Splitting them into completely separate directories and state files minimizes the blast radius of a failed deployment.

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
