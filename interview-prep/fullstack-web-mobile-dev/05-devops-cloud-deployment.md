# 05 - DevOps & Cloud Deployment

## Containerization

### 1. What is Docker and why is it useful for Full-Stack development?
**Answer:**
A tool to package applications into "Containers" that include the code, runtime, libraries, and config. 
- **Benefit:** Ensures the app runs the same in local dev, staging, and production. No "it works on my machine" issues.

### 2. Difference between `Docker Image` and `Docker Container`?
**Answer:**
- **Image:** A read-only template with instructions for creating a container (like a Class).
- **Container:** A running instance of an image (like an Object).

### 3. What is "Docker Compose"?
**Answer:**
A tool for defining and running multi-container applications (e.g., a React app + Node.js API + Postgres DB) using a single YAML file.

## Cloud & CI/CD

### 4. What is CI/CD (Continuous Integration / Continuous Deployment)?
**Answer:**
- **CI:** Automatically building and testing code on every push to catch bugs early.
- **CD:** Automatically deploying the tested code to production to deliver features faster.

### 5. Explain "Horizontal" vs. "Vertical" Scaling in the Cloud.
**Answer:**
- **Horizontal:** Adding more instances/machines (e.g., adding more EC2 instances).
- **Vertical:** Adding more resources (CPU/RAM) to a single machine.

### 6. What is "Serverless" computing (e.g., AWS Lambda)?
**Answer:**
A cloud-computing model where the provider manages the server infrastructure. You only upload the code (functions) and pay for the execution time. It scales automatically but can have "cold start" latency.
