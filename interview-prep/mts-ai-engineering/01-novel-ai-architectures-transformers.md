# 01 - Novel Architectures & Transformers

## Transformer Innovations

### 1. Explain the evolution from Vanilla Transformers to modern LLMs (e.g., Llama 3).
**Answer:**
- **Pre-normalization:** Moving LayerNorm to before the attention/MLP blocks (RMSNorm).
- **SwiGLU Activation:** Replacing ReLU for better performance.
- **RoPE (Rotary Positional Embeddings):** Replacing absolute or relative learned embeddings for better extrapolation.
- **Grouped-Query Attention (GQA):** Balancing performance of Multi-Query and Multi-Head attention.
- **KV Caching:** Optimizing inference speed.

### 2. What are "Scaling Laws" (Chinchilla) and why do they matter?
**Answer:**
Scaling laws describe how model performance (Loss) relates to the number of parameters ($N$), the amount of training data ($D$), and the compute ($C$).
- **Chinchilla Result:** Many models were "under-trained." For a fixed compute budget, it's often better to train a smaller model on more data than a larger model on less data.

### 3. What is "Mixture of Experts" (MoE)?
**Answer:**
A sparse architecture where only a subset of the model's parameters (the "Experts") are activated for any given input.
- **Benefit:** Allows for massive parameter counts (e.g., 1.8T) with the inference cost of a much smaller model.
- **Challenge:** Harder to train stably and requires specialized load balancing (Routing).

## Novel Architectures

### 4. What is the "State Space Model" (SSM) approach (e.g., Mamba)?
**Answer:**
An alternative to Transformers that offers linear scaling ($O(N)$) with context length, compared to Transformer's quadratic scaling ($O(N^2)$). It uses selective scanning to maintain a compressed "hidden state" that captures context.

### 5. Importance of "Long Context" architectures?
**Answer:**
Allowing models to process entire books, codebases, or long videos in a single prompt. Techniques include **Ring Attention**, **FlashAttention**, and **Linear Attention** variations.

### 6. What is "Cross-Attention" vs. "Self-Attention"?
**Answer:**
- **Self-Attention:** Comparing tokens within the same sequence (standard for Decoders).
- **Cross-Attention:** Comparing tokens from one sequence (Encoder) to another (Decoder). Essential for translation and multi-modal (Image-to-Text) tasks.
