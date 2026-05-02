# 03 - Enterprise Solution Architecture

## High-Level Design

### 1. What are the core components of an Enterprise AI Architecture?
**Answer:**
- **Data Ingestion/Storage:** (Data Lake/S3).
- **Processing/Feature Layer:** (Spark/DBT).
- **Model Layer:** (LLM APIs or custom ML clusters).
- **Orchestration Layer:** (LangChain/Airflow).
- **Application Layer:** (FastAPI/React).
- **Observability/Security:** (MLflow/IAM).

### 2. Explain RAG (Retrieval-Augmented Generation) at a high level.
**Answer:**
Instead of relying only on the LLM's static training data, RAG retrieves relevant documents from a company's private database (using a Vector DB) and "stuffs" them into the prompt. This reduces hallucinations and provides ground truth based on proprietary data.

### 3. How do you handle "Vendor Lock-in"?
**Answer:**
- Use **abstraction layers** (e.g., LiteLLM or custom wrappers) to make switching between providers (OpenAI, Anthropic, Gemini) easier.
- Favor **open standards** (ONNX, Docker, Kubernetes).
- Use **multi-cloud** strategies for critical storage/compute.

## Non-Functional Requirements

### 4. How do you design for AI Latency?
**Answer:**
- **Streaming:** Show the first token immediately.
- **Asynchronous tasks:** Use Celery/BullMQ for tasks that take >5 seconds.
- **Caching:** Cache common prompts/responses.
- **Model Distillation:** Use smaller, faster models for simple classification tasks.

### 5. What is "Human-in-the-loop" (HITL) architecture?
**Answer:**
A system design where high-confidence predictions are automated, but low-confidence predictions are routed to a human queue for verification. The human's decision is then used to retrain the model.

### 6. Explain "Cold Start" and "Scaling" for a consulting client.
**Answer:**
Explain that AI systems need "warm-up" time for both data (filling the vector DB) and infrastructure (server initialization). Scaling is handled by auto-scaling groups in the cloud (AWS/Azure).
