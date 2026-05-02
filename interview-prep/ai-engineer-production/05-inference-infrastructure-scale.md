# 05 - Inference Infrastructure & Scale

## Serving Architectures

### 1. What are vLLM and TGI (Text Generation Inference)?
**Answer:**
High-performance serving engines for open-source LLMs (Llama, Mistral).
- **vLLM:** Uses **PagedAttention** to efficiently manage KV cache memory, allowing for much higher throughput (up to 24x more) than standard Hugging Face implementations.
- **TGI:** Developed by Hugging Face, optimized for production deployment with features like continuous batching and token streaming.

### 2. Explain "Continuous Batching" (Iteration-level batching).
**Answer:**
Traditional batching waits for all requests in a batch to finish. Continuous batching schedules new requests as soon as any request in the current batch finishes a single token generation cycle. This maximizes GPU utilization and reduces latency.

### 3. How do you optimize inference for Cost?
**Answer:**
- **Quantization:** Using 4-bit or 8-bit versions of models (AWQ, GPTQ).
- **Smaller Models:** Using Llama-3-8B or Mistral-7B for simple tasks.
- **Caching:** Using a semantic cache (e.g., GPTCache) to store and reuse responses for similar queries.
- **Speculative Decoding:** Using a small, fast model to predict tokens and a large model to verify them in parallel.

## Cloud Infrastructure

### 4. How do you deploy an LLM on AWS SageMaker?
**Answer:**
- Use **SageMaker JumpStart** for pre-trained models.
- Create a **SageMaker Real-time Inference Endpoint**.
- Use **SageMaker Multi-Model Endpoints** if you need to serve multiple smaller models on one instance to save costs.

### 5. What is "Cold Start" in LLM serving?
**Answer:**
The time taken to download the model (often 10GB+) and load it into GPU memory. Mitigation:
- Keep instances warm (Provisioned Concurrency).
- Use optimized container images.
- Store model weights in a high-speed storage like FSx for Lustre.

### 6. Explain "Rate Limiting" at the Infrastructure level.
**Answer:**
Using an API Gateway (like AWS API Gateway or Kong) to limit requests per user/API key. This protects your inference servers from being overwhelmed and allows for tiered pricing (e.g., Free vs. Pro).
