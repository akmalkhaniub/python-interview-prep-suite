# 05 - Inference Optimization & Serving

## Inference Optimization

### 1. What is "Speculative Decoding"?
**Answer:**
Using a small, fast "Draft" model to predict tokens and then having the large "Target" model verify them in parallel. If the draft is correct, multiple tokens are generated in a single step, significantly increasing speed.

### 2. How does "FlashAttention" work?
**Answer:**
An IO-aware exact attention algorithm that minimizes memory reads/writes by tiling the attention calculation and using SRAM. It's much faster and uses less memory than standard attention.

### 3. What is "Quantization" (GPTQ, AWQ, GGUF)?
**Answer:**
Reducing the precision of model weights (e.g., from 16-bit to 4-bit) to save memory and increase speed.
- **GPTQ:** Post-training quantization.
- **AWQ:** Activation-aware quantization, often better at preserving accuracy.
- **GGUF:** Standard format for CPU/Llama.cpp inference.

## Serving at Scale

### 4. What is "KV Caching" and why is it mandatory for LLMs?
**Answer:**
Storing the Key and Value tensors for previous tokens so they don't have to be recomputed for every new token generation. This makes inference $O(N)$ instead of $O(N^2)$.

### 5. Importance of "Continuous Batching"?
**Answer:**
Instead of waiting for an entire batch to finish, new requests are added to the batch as soon as a request in the current batch finishes. This significantly increases throughput in high-traffic scenarios (e.g., vLLM).

### 6. What is "PagedAttention"?
**Answer:**
A memory management technique (from vLLM) that stores KV caches in non-contiguous memory blocks (similar to Virtual Memory in OS), eliminating memory fragmentation and allowing for much larger batch sizes.
