# 01. Transformer Architectures & Scaling Laws

A researcher must understand the mathematical and structural evolution of Transformers to push their limits.

## 🏗️ Evolution of Attention Mechanisms
The original "Attention is All You Need" (2017) has evolved significantly for efficiency and scale.

### 1. Self-Attention Variants
- **Multi-Head Attention (MHA):** The baseline. Each head has its own Q, K, V weights.
- **Multi-Query Attention (MQA):** Multiple Q heads share a single K and V head. drastically reduces KV cache size during inference.
- **Grouped-Query Attention (GQA):** An intermediate approach (used in Llama 2/3). Q heads are grouped, and each group shares one K/V head. Balances performance and KV cache efficiency.
- **Flash Attention:** A hardware-aware algorithm that reduces IO by computing attention in blocks that fit in GPU SRAM.

### 2. Positional Encodings
How a model understands the order of tokens is critical for long-context research.
- **Sinusoidal:** Absolute positions (fixed).
- **RoPE (Rotary Positional Embeddings):** Multiplies Q and K by a rotation matrix based on position. Preserves relative distance and generalizes better to longer sequences.
- **ALiBi (Attention with Linear Biases):** Biases the attention score based on distance. Allows for training on short sequences and zero-shot extrapolation to much longer ones.

### 3. Normalization & Activation
- **RMSNorm:** A faster alternative to LayerNorm that only scales by the Root Mean Square of the activations.
- **SwiGLU:** A gated linear unit activation function that has become standard for state-of-the-art LLMs.

---

## 📈 Scaling Laws (Chinchilla & Beyond)
Researching "novel architectures" requires understanding when to scale parameters vs. data.

### 1. The Chinchilla Finding (Hoffmann et al., 2022)
- Most large models (like GPT-3) were **under-trained**.
- **The Law:** For every doubling of model size, the training tokens should also double.
- **Compute-Optimal:** A 70B model needs ~1.4 Trillion tokens to be compute-optimal.

### 2. Over-training & Frontier Models
- Modern models (Llama 3) often use **far more** tokens than Chinchilla suggests (e.g., 15T tokens for an 8B model).
- This is done to maximize **inference-time efficiency** (a smaller, smarter model is cheaper to serve).

---

## ❓ Research Interview Questions
1. **Derive the complexity of self-attention.**
   - *Answer:* $O(n^2 \cdot d)$, where $n$ is sequence length and $d$ is embedding dimension. This is why long context is hard.
2. **Explain why GQA is preferred over MHA for 70B+ parameter models.**
   - *Answer:* At that scale, the KV cache (storing Keys and Values for all layers/heads) becomes a memory bottleneck during inference. GQA reduces this memory footprint significantly while maintaining accuracy.
3. **What happens to a model if you double the training data but keep the parameters constant?**
   - *Answer:* Perplexity will continue to drop, but at a diminishing rate. You are moving closer to the "irreducible loss" for that specific architecture's capacity.

---
**Next Step:** Learn how we guide these models after training in [02-model-alignment-rlhf.md](02-model-alignment-rlhf.md).
