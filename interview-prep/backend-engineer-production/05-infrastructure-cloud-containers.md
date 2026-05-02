# 05 - Infrastructure, Cloud & Containers

## Containerization

### 1. What is Docker and why use it?
**Answer:**
A tool to package an app and its dependencies into a single "Container." It ensures the app runs identically in development, staging, and production.

### 2. What is Kubernetes (K8s)?
**Answer:**
A container orchestration platform. It handles deployment, scaling, and management of containerized applications. Key features: Self-healing, Load Balancing, and Rolling Updates.

### 3. Difference between a "Stateful" and "Stateless" application?
**Answer:**
- **Stateless:** The application doesn't store any data locally (e.g., in memory or local disk). All state is in an external DB/Cache. These are easy to scale horizontally.
- **Stateful:** The application relies on local storage (e.g., a DB itself). Harder to scale and move between nodes.

## Cloud Strategy

### 4. What is "Serverless" (AWS Lambda / Google Cloud Functions)?
**Answer:**
A model where the cloud provider manages the infrastructure. You only write the code (functions). You pay only for the execution time. Great for event-driven tasks or low-traffic APIs.

### 5. Importance of "Infrastructure as Code" (Terraform / CloudFormation)?
**Answer:**
Defining your infrastructure (servers, DBs, networks) in configuration files.
- **Benefit:** Reproducible environments, version control for infrastructure, and avoids "manual drift" in settings.

### 6. What is a "Service Mesh" (Istio / Linkerd)?
**Answer:**
A dedicated infrastructure layer that handles service-to-service communication, providing security (mTLS), observability, and traffic management without changing the application code.
