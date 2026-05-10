# Technical Study Guide: MLE Bench (Turing)

## 1. MLE-Bench & Frontier Evaluation
- **Task Design:** Understanding the "Agentic" capabilities required for an AI to act as an engineer (File navigation, terminal usage, code execution).
- **Benchmark Metrics:** Pass@1, Pass@k, and how to measure "correctness" in non-deterministic ML tasks.
- **Data Contamination:** Strategies to ensure benchmark tasks aren't already in the model's training set.

## 2. Advanced PyTorch & JAX
- **PyTorch:**
    - Distributed Data Parallel (DDP) and RPC for distributed training.
    - Custom Autograd functions and Hooks for debugging.
    - Profiling and identifying bottlenecks using `torch.profiler`.
- **JAX:**
    - XLA (Accelerated Linear Algebra) compilation.
    - Pmap vs. Vmap for parallelization.

## 3. Production ML Engineering
- **Pipelines:** Designing modular pipelines that separate Data Loading, Training, and Evaluation.
- **Environment Management:** Docker, Conda, and `requirements.txt` locking for absolute reproducibility.
- **Logging & Observability:** Weights & Biases (W&B), MLflow, or TensorBoard for tracking benchmark runs.

## 4. Debugging & Performance
- **Common ML Bugs:** Vanishing/Exploding gradients, shape mismatches, and data leakage.
- **Refactoring:** Applying DRY (Don't Repeat Yourself) and SOLID principles to research-grade code to make it production-ready.
- **Hardware Optimization:** Maximizing GPU utilization and handling CUDA Out-of-Memory (OOM) errors.

## 5. Frontier Model Capabilities
- **LLM Reasoning:** Chain-of-Thought (CoT) and Self-Correction patterns in benchmark tasks.
- **Multimodal Evaluation:** How to evaluate models that handle both image/video and code.

## 6. Real-world ML Systems
- **Inference Servers:** Triton Inference Server or vLLM architecture.
- **Dataset Preparation:** Efficient streaming of massive datasets for evaluation (e.g., Hugging Face `datasets` library).

## Resources to Review
- [OpenAI: MLE-bench: Evaluating Machine Learning Agents](https://openai.com/index/mle-bench/)
- [SWE-bench: Can Language Models Resolve Real-World GitHub Issues?](https://www.swebench.com/)
- [Full Stack Deep Learning: Deployment & Monitoring](https://fullstackdeeplearning.com/)
- [PyTorch Performance Tuning Guide](https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html)
- [JAX 101: The Basics](https://jax.readthedocs.io/en/latest/jax-101/index.html)
