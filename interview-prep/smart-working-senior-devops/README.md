# Study Plan: Senior AWS DevOps Engineer (Smart Working)

This module focuses on building and scaling cloud-native infrastructure on AWS using **Terraform**, **Kubernetes (EKS)**, and **GitLab CI/CD**.

## 🎯 Role Overview
The role is "hands-on architect" in a small platform team. You need to bridge the gap between development and production operations by:
1.  **Automating Everything:** Using Terraform for IaC and Ansible/Packer for AMIs.
2.  **Orchestrating Containers:** Managing EKS workloads and production troubleshooting.
3.  **Observing the System:** Setting up Grafana/Prometheus and centralized logging (Datadog/Splunk).
4.  **Securing the Cloud:** Implementing IAM, KMS, and Secrets Manager best practices.

## 🛠️ Tech Stack Focus
*   **AWS:** VPC, EKS, RDS, Lambda, EventBridge, SQS/SNS.
*   **IaC:** Terraform (Modules, State management, Terragrunt).
*   **CI/CD:** GitLab CI (YAML, Runners, Artifacts, Environments).
*   **Automation:** Ansible (Playbooks) & Packer (HCL templates).
*   **Observability:** Prometheus, Grafana, ELK/Splunk.

## 🗺️ Learning Roadmap

### 1. AWS Infrastructure & Terraform
*   [01-aws-infrastructure-terraform.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\smart-working-senior-devops\01-aws-infrastructure-terraform.md)
*   **Topics:** VPC Design, Terraform Module best practices, State Locking, Cross-Account IAM.

### 2. Kubernetes (EKS) in Production
*   [02-kubernetes-eks-production.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\smart-working-senior-devops\02-kubernetes-eks-production.md)
*   **Topics:** Managed Node Groups, Ingress Controllers (ALB), Cluster Autoscaler, Karpenter.

### 3. CI/CD with GitLab & Automation
*   [03-cicd-gitlab-automation.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\smart-working-senior-devops\03-cicd-gitlab-automation.md)
*   **Topics:** GitLab CI/CD YAML, Packer for AMIs, Ansible for configuration management.

### 4. Observability & Performance Tuning
*   [04-observability-performance-tuning.md](file:///g:/ReplitProjects\telemedicine\python-engineer-notebooks\interview-prep\smart-working-senior-devops\04-observability-performance-tuning.md)
*   **Topics:** Prometheus/Grafana stack, Log aggregation (ELK/Splunk), RDS/ClickHouse optimization.

## 🚀 Key Interview Questions
1.  "How do you handle Terraform state conflicts in a multi-developer environment?"
2.  "Describe your strategy for zero-downtime EKS cluster upgrades."
3.  "How would you build a CI/CD pipeline in GitLab that deploys to multiple AWS accounts securely?"
4.  "What are the trade-offs between using Ansible vs. UserData scripts for EC2 initialization?"
