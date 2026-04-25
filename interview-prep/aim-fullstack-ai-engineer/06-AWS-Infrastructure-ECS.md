# AWS Infrastructure & Cloud Architecture
**Focus:** AWS ECS, RDS, S3, CloudFront, Cognito, IaC

---

## Section A: Container Orchestration & Security

### Q1: Architecting Highly Available ECS Deployments
**Question:** How do you architect a highly available, containerized Node.js application using AWS ECS (Fargate)?

**Expected Answer:**
Architecting for High Availability on ECS Fargate starts with a robust Virtual Private Cloud (VPC) design. I deploy the ECS Tasks into **Private Subnets** spanning at least two Availability Zones (AZs) to ensure that an entire AWS data center outage won't bring down the system. 

Because Fargate is serverless compute, the orchestrator handles underlying EC2 patching. To manage incoming traffic, I place an **Application Load Balancer (ALB)** in the Public Subnets. The ALB terminates SSL/TLS, balances traffic across the ECS tasks in the private subnets, and conducts continuous Health Checks. If a Node.js container crashes due to an unhandled exception, the ALB marks it unhealthy, stops routing traffic to it, and ECS automatically spins up a replacement container to maintain the defined desired task count. Finally, I define an Application Auto Scaling policy attached to the ECS Service to scale the task count dynamically based on CPU utilization or incoming request volume.

### Q2: Securing the VPC and Application Load Balancer
**Question:** Describe the role of Security Groups, the Application Load Balancer, and VPC Subnets in securing an ECS deployment.

**Expected Answer:**
Security in AWS is implemented via strict boundary layers. At the network level, the VPC is divided into Public and Private Subnets. The ECS containers housing the proprietary business logic and the RDS database housing sensitive healthcare data are placed exclusively in Private Subnets; they have no public IP addresses and cannot be routed to directly from the open internet.

The **Application Load Balancer (ALB)** acts as the sole ingress point in the Public Subnet. I configure **Security Groups** as virtual firewalls to enforce strict traffic rules: The ALB Security Group is configured to only allow inbound traffic on Port 443 (HTTPS) from the internet `0.0.0.0/0`. Crucially, the ECS Security Group is configured to explicitly deny all inbound traffic *except* traffic originating specifically from the ALB Security Group. This guarantees that a bad actor cannot bypass the Load Balancer (and its associated AWS WAF protections) to hit the application containers directly.

---

## Section B: Cloud Services & IaC

### Q3: Serving Static Assets via S3 and CloudFront
**Question:** Explain your strategy for serving static assets and media securely using Amazon S3 and CloudFront.

**Expected Answer:**
Serving heavy static assets (like React bundles, images, or PDF reports) directly from Node.js Express is an anti-pattern that wastes expensive compute cycles. Instead, I store all static and media files in an **Amazon S3** bucket. 

To optimize delivery globally, I place **Amazon CloudFront** (AWS's Content Delivery Network) in front of the S3 bucket. CloudFront caches the files at edge locations physically closer to the user, massively reducing latency and data transfer costs. For security, I do not make the S3 bucket public. Instead, I configure an **Origin Access Control (OAC)** policy on the bucket, restricting access so that the files can *only* be read if the request passes through CloudFront. For sensitive user-uploaded documents (like patient care plans), I utilize CloudFront Signed URLs, ensuring that only authenticated users with a temporary, time-bound cryptographic signature can access the specific file.

### Q4: Managing Authentication with AWS Cognito
**Question:** How do you manage user authentication and authorization across mobile and web applications using AWS Cognito?

**Expected Answer:**
Implementing custom password hashing and session management in-house is a massive security risk, especially in healthcare. I offload identity management entirely to **AWS Cognito User Pools**. Cognito handles secure user registration, login, password resets, and Multi-Factor Authentication (MFA) out of the box.

When a user logs into the Next.js or Flutter frontend, Cognito authenticates them and returns standard OAuth2/OIDC JWT tokens (ID, Access, and Refresh tokens). The frontend includes the Access JWT in the `Authorization` header of subsequent API requests to the Node.js backend. The backend Express middleware utilizes a library (like `aws-jwt-verify`) to cryptographically validate the token's signature against Cognito's public JWKS keys without executing a database query. For Authorization (RBAC), I map Cognito Custom Attributes or Cognito Groups (e.g., `Admin`, `CareWorker`) directly into the JWT payload, allowing the backend to instantly reject unauthorized requests based on the user's role.

### Q5: Infrastructure as Code (IaC)
**Question:** How do you handle infrastructure deployments and configuration drift using Infrastructure as Code (Terraform or AWS CDK)?

**Expected Answer:**
Deploying AWS infrastructure via the manual "ClickOps" method in the AWS Console is unscalable and guarantees environment inconsistency between Staging and Production. I strictly utilize **Infrastructure as Code (IaC)**, specifically Terraform or AWS CDK, to define the entire cloud architecture as version-controlled code.

Using Terraform, I define declarative modules for the VPC, ECS clusters, and RDS databases. When an infrastructure change is required (e.g., scaling up the database instance size), a developer modifies the `.tf` file and opens a Pull Request. The CI/CD pipeline runs `terraform plan` to output a deterministic diff of exactly what AWS resources will be created, modified, or destroyed. Once merged, the pipeline executes `terraform apply`. This guarantees that infrastructure deployments are peer-reviewed, easily repeatable for disaster recovery, and completely eliminates "configuration drift" because the codebase is the ultimate source of truth for the cloud environment.
