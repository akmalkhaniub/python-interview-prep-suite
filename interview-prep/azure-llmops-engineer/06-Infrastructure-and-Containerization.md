# 06: Infrastructure and Containerization

The plumbing that holds the AI system together.

## 1. Containerization (Docker)
*   **Why?**: Consistency across Dev, Test, and Prod. Isolation of Python dependencies (avoiding "it works on my machine").
*   **Best Practices**: Small base images (e.g., `python:3.9-slim`), multi-stage builds, and non-root users for security.

## 2. Kubernetes (AKS)
*   **Deployment**: Scaling your RAG API based on traffic.
*   **Resource Management**: Setting CPU and Memory requests/limits for AI workloads.
*   **Health Checks**: Implementing Liveness and Readiness probes to ensure the container is ready to handle LLM calls.

## 3. Reusable MLOps Templates
*   **Standardization**: Providing other teams with pre-built GitHub Action workflows or Bicep templates for deploying a standard RAG stack.
*   **Documentation**: Maintaining 'Runbooks' for common issues like token limit exhaustion or vector search indexing failures.

## Interview Questions
1.  **"Why would you choose AKS over Azure App Service for a RAG application?"**
    *   *Ans*: AKS provides better scaling control, more complex networking options (like service meshes), and is more cost-effective if you're running many related microservices (e.g., an ingestion service, an extraction service, and a chat service).
2.  **"How do you handle secrets in a containerized AI app on Azure?"**
    *   *Ans*: I use Azure Key Vault integration. In AKS, I use the Secret Store CSI driver to mount secrets as files or environment variables directly from Key Vault, so they are never stored in the image or Git.
3.  **"What is the advantage of a multi-stage Docker build for a Python AI app?"**
    *   *Ans*: It keeps the final image small by using a build stage to install compilers and build dependencies, and then copying only the final artifacts to a clean production image.

## Practice Task
*   Write a basic `Dockerfile` for a FastAPI application that uses LangChain.
