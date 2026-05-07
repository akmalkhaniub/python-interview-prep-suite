# Deep Dive: AWS Infrastructure & Terraform

## 🏗️ VPC Design for High Availability
*   **Multi-AZ:** Deploy resources across at least 3 Availability Zones.
*   **Subnet Strategy:** 
    *   **Public:** Bastion hosts, ALBs, NAT Gateways.
    *   **Private:** EKS worker nodes, RDS, internal services.
    *   **Database-only:** Isolated subnets for RDS with no internet route.
*   **Connectivity:** VPC Peering vs Transit Gateway (for multi-account environments).

## 🛠️ Terraform Best Practices
*   **State Management:** Remote backend (S3) with State Locking (DynamoDB).
*   **Modules:** Build reusable modules (e.g., `vpc`, `eks`, `rds`). Avoid hardcoded values; use variables and outputs.
*   **Workspaces:** For environment separation (dev, staging, prod), though separate state files per environment is often preferred for isolation.
*   **Terragrunt:** (Nice to have) A wrapper to keep Terraform code DRY (Don't Repeat Yourself).

## 🔒 Security in IaC
*   **Least Privilege:** Use IAM roles for EC2/EKS pods (IRSA) instead of long-lived access keys.
*   **Encryption:** KMS for EBS, S3, and RDS encryption at rest.
*   **Secrets:** Never hardcode secrets. Use AWS Secrets Manager or Parameter Store (SSM) and inject them at runtime.

## 💡 Interview Q&A
**Q: How do you handle a scenario where `terraform apply` fails halfway through?**
**A:** Check the `terraform state` to see which resources were created. Fix the root cause (e.g., AWS limit reached, syntax error) and run `terraform plan` again to see the delta. Use `terraform refresh` or `terraform import` if resources were created but aren't in the state.

**Q: Why use Terraform over AWS CloudFormation?**
**A:** Terraform is provider-agnostic (can manage GitLab, Datadog, and AWS in the same plan), has a more readable HCL syntax, and supports "State" which allows for better change tracking and planning.

## 🛠️ Code Snippet: S3 Backend Configuration
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "dev/network/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock-table"
    encrypt        = true
  }
}
```
