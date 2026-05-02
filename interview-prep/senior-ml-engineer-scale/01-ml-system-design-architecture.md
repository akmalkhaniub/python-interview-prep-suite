# 01 - ML System Design & Architecture

## Scalable Inference

### 1. How do you decide between Online (Real-time) vs. Batch Inference?
**Answer:**
- **Online:** Low latency required (<100ms). Use for personalization, fraud detection. Challenges: Scaling, high availability, cold starts.
- **Batch:** High throughput, latency is secondary. Use for nightly recommendations, report generation. Challenges: Data staleness, scheduling large jobs.
- **Streaming (Hybrid):** Processes data as it arrives (e.g., Flink) for "near real-time" features.

### 2. How do you scale a model to handle 10,000 Requests Per Second (RPS)?
**Answer:**
- **Horizontal Scaling:** Deploy multiple model instances behind a Load Balancer.
- **Model Optimization:** Convert to TensorRT/ONNX, use Quantization/Pruning.
- **Caching:** Cache predictions for common inputs (e.g., Redis).
- **Asynchronous Processing:** Use a message queue (Kafka/RabbitMQ) for non-critical predictions.
- **GPU Sharing:** Use technologies like NVIDIA Triton Inference Server to maximize GPU utilization.

### 3. What is a Feature Store and why is it needed?
**Answer:**
A central repository for managing, discovering, and serving features.
- **Consistency:** Ensures the same feature code is used for training and serving (preventing offline-online skew).
- **Reuse:** Avoids recomputing common features (e.g., "user_click_rate_7d") across different teams.
- **Point-in-time Correctness:** Allows retrieving feature values at a specific timestamp for training.

## Training Pipelines

### 4. How do you handle Data Leakage in time-series models?
**Answer:**
Ensure that information from the future is not available in the training set.
- **Time-based Splitting:** Use a cutoff point (e.g., Train on Jan-June, Test on July). Do NOT use random shuffling.
- **Feature Calculation:** Ensure features like "rolling mean" only include data available *before* the prediction timestamp.

### 5. Explain Distributed Training (Data Parallelism vs. Model Parallelism).
**Answer:**
- **Data Parallelism:** The model is replicated on multiple GPUs. Each GPU processes a different slice of data, and gradients are averaged (e.g., PyTorch DDP).
- **Model Parallelism:** The model is too large for one GPU, so different layers are placed on different GPUs. Necessary for Large Language Models (LLMs).
