# 04 - AWS & DevOps Essentials

## AWS Services

### 1. Difference between EC2, Lambda, and S3?
**Answer:**
- **EC2 (Elastic Compute Cloud):** Virtual machines. You manage the OS. Good for long-running processes or custom environments.
- **Lambda:** Serverless functions. Pay per execution. Good for event-driven tasks or small APIs.
- **S3 (Simple Storage Service):** Object storage. Good for static files, images, logs, and backups.

### 2. What is an RDS (Relational Database Service)?
**Answer:**
A managed database service for Postgres, MySQL, etc. AWS handles patching, backups, and scaling, allowing you to focus on application logic.

### 3. What is VPC (Virtual Private Cloud)?
**Answer:**
A private network in AWS. You control IP ranges, subnets, and routing. Used to isolate resources (e.g., keeping your database in a private subnet not accessible from the internet).

## DevOps & CI/CD

### 4. What is Docker and why use it?
**Answer:**
A platform for containerizing applications.
- **Why:** "It works on my machine" problem solved. Containers package code + dependencies + runtime. They are lightweight, portable, and start quickly.

### 5. Explain CI/CD.
**Answer:**
- **Continuous Integration (CI):** Automatically building and testing code on every push.
- **Continuous Deployment (CD):** Automatically deploying the tested code to production.
- **Tools:** GitHub Actions, Jenkins, CircleCI.

### 6. What is Infrastructure as Code (IaC)?
**Answer:**
Managing infrastructure using configuration files (Terraform, AWS CDK, CloudFormation). This makes infrastructure versionable, repeatable, and less prone to human error during manual console clicks.
