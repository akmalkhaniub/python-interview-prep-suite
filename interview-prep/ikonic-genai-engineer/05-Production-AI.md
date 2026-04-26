# 05: Production AI Pipelines (MLOps)

Moving from a Jupyter Notebook to a scalable production environment.

## 1. FastAPI for AI Services
The standard for serving LLM pipelines.
- **Asynchronous endpoints:** To handle multiple concurrent LLM requests.
- **Streaming:** Using `StreamingResponse` to provide a "ChatGPT-like" experience.

## 2. Dockerizing AI Applications
- **Base Image:** Using `python:3.10-slim` or NVIDIA-optimized images for GPU support.
- **Dependency Management:** handling heavy libraries like `torch` or `llama-cpp`.

## 3. Scaling & Infrastructure
- **Load Balancing:** Distributing requests across multiple LLM server instances.
- **GPU Orchestration:** Using Kubernetes (K8s) or cloud-specific services (AWS SageMaker, Azure ML).
- **Caching:** Using Redis to cache common queries and save on API costs.

## 4. Monitoring & Logging
- **Token Usage:** Tracking how much each user/feature is costing.
- **Latency Monitoring:** Alerts for when the LLM takes too long to respond.
- **Drift Detection:** Monitoring if the model's output quality changes over time.

## 5. Potential Interview Questions
1. **How would you deploy an LLM on AWS?**
   - *Answer:* 
     - **Option 1:** SageMaker Endpoints (Managed, expensive).
     - **Option 2:** ECS/EKS with GPU-enabled instances (More control).
     - **Option 3:** Serverless (AWS Lambda) for small tasks (fast, but cold start issues).
2. **Explain "Rate Limiting" in the context of LLM APIs.**
   - *Answer:* OpenAI/Anthropic have TPM (Tokens Per Minute) and RPM (Requests Per Minute) limits. I'd implement a queue (RabbitMQ/Redis) to buffer requests if we hit those limits.
3. **What is the benefit of using an "API Gateway" for AI services?**
   - *Answer:* It handles authentication, rate limiting, logging, and routing to different models (e.g., routing simple tasks to GPT-3.5 and complex ones to GPT-4).
