# Deep Dive: CI/CD with GitLab & Automation

## 🦊 GitLab CI/CD YAML Components
*   **Stages:** (e.g., build, test, terraform-plan, terraform-apply, deploy).
*   **Job Rules:** Using `rules: if` to trigger jobs based on branch names or tags.
*   **Artifacts:** Passing files (e.g., compiled binaries, TF plan files) between stages.
*   **Cache:** Speeding up jobs by caching `node_modules` or `pip` packages.
*   **Runners:** Shared vs. Private (EC2-hosted) runners for better security and performance.

## 📦 Automation with Packer & Ansible
The JD specifically mentions **Packer** and **Ansible** for AMI creation:
*   **Packer:** Builds the "Golden Image" (AMI) by launching an EC2, running scripts, and taking a snapshot.
*   **Ansible:** The "provisioner" within Packer. It installs software (Docker, CloudWatch Agent, Monitoring tools) in a declarative way.
*   **Workflow:**
    1. GitLab triggers Packer.
    2. Packer uses Ansible to configure a temporary EC2.
    3. Packer creates an AMI.
    4. Terraform uses the new AMI ID to update the Auto Scaling Group (ASG).

## 🛠️ Infrastructure CI/CD Best Practices
*   **Plan First:** Always run `terraform plan` and store the output in an artifact.
*   **Approval Gate:** Use manual triggers for `terraform apply` in production.
*   **Drift Detection:** Run a scheduled GitLab job to check if the manual changes (drift) happened in the AWS Console.

## 💡 Interview Q&A
**Q: Why use Packer instead of just using Ansible on a live instance?**
**A:** "Immutable Infrastructure". With Packer, you build the image once and deploy it many times. It reduces "Configuration Drift" and makes scaling much faster because instances don't need to run setup scripts on boot.

**Q: How do you handle secrets in a GitLab CI pipeline?**
**A:** Use **GitLab CI Variables** (masked and protected). For better security, integrate GitLab with **AWS Secrets Manager** or **HashiCorp Vault** so secrets are fetched at runtime via a JWT token.

## 🛠️ Code Snippet: Basic GitLab CI for Terraform
```yaml
stages:
  - plan
  - apply

tf-plan:
  stage: plan
  script:
    - terraform init
    - terraform plan -out=tfplan
  artifacts:
    paths:
      - tfplan

tf-apply:
  stage: apply
  script:
    - terraform apply tfplan
  when: manual
  only:
    - main
```
