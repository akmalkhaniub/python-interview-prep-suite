# Interview Prep: AI/ML Researcher (Transformers & Alignment)

This module is designed for the **AI/ML Researcher** role, focusing on the scientific advancement of model capabilities, alignment, and evaluation frameworks.

## 🔬 Role Overview
Unlike the AI Engineer, the Researcher is expected to look "under the hood" of the transformer architecture, design rigorous experiments, and communicate findings with academic precision.

## 🗺️ Study Roadmap

### 1. Transformer Internals & Scaling ([01-transformer-architectures-scaling.md](01-transformer-architectures-scaling.md))
- **Attention Deep Dive:** Multi-head, Multi-query (MQA), Grouped-query (GQA), and Flash Attention.
- **Normalization & Encodings:** RMSNorm, RoPE (Rotary Positional Embeddings), and ALiBi.
- **Scaling Laws:** Understanding Chinchilla scaling laws and compute-optimal training.

### 2. Model Alignment & Steering ([02-model-alignment-rlhf.md](02-model-alignment-rlhf.md))
- **RLHF Pipeline:** SFT (Supervised Fine-Tuning) -> Reward Modeling -> PPO.
- **DPO (Direct Preference Optimization):** The math and advantages of removing the explicit reward model.
- **Safety & Jailbreaking:** Researching model boundaries and constitutional AI.

### 3. Rigorous Evaluation ([03-eval-frameworks-benchmarking.md](03-eval-frameworks-benchmarking.md))
- **Benchmark Design:** How to create robust, contamination-free datasets for model evaluation.
- **Metrics:** Beyond perplexity—MMLU, HumanEval, and custom internal benchmarks.
- **Statistical Significance:** Ensuring research findings aren't just noise.

### 4. Large-Scale Training ([04-pytorch-distributed-training.md](04-pytorch-distributed-training.md))
- **Distributed PyTorch:** FSDP, DDP, and Pipeline Parallelism.
- **Memory Efficiency:** Gradient Checkpointing, 8-bit optimizers, and Mixed Precision (FP16/BF16).
- **Compute Optimization:** GPU kernel optimization and throughput bottlenecks.

### 5. Research Communication ([05-research-technical-writing.md](05-research-technical-writing.md))
- **Academic Writing:** Structuring research papers and internal "RFCs" for new architectures.
- **Visualization:** Effective ways to present loss curves, ablation studies, and evaluation results.

## 🧪 Practical Practice
- **Research Lab Notebook:** ([06-research-lab-notebook.ipynb](06-research-lab-notebook.ipynb)) implements a minimal Transformer block and an alignment experiment logic.

---

> [!TIP]
> This role prizes **Rigor**. Be prepared to derive attention mechanisms on a whiteboard or explain exactly why a specific normalization layer was chosen for a model like Llama 3.
