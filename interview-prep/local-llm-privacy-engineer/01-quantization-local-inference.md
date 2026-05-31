# Module 01: Quantization & Local Inference

This module covers model compression techniques, GPU/CPU memory calculation, high-performance local inference engines, and CUDA optimization.

---

## Technical Q&A

### Q1: Compare the quantization formats GGUF, AWQ, and GPTQ. When would you select each for an on-premises deployment?
**Answer:**

| Feature | GGUF (GPT-Generated Unified Format) | AWQ (Activation-aware Weight Quantization) | GPTQ (Generalized Post-Training Quantization) |
| :--- | :--- | :--- | :--- |
| **Primary Execution Hardware** | CPU-first or Hybrid (CPU/GPU offloading). | GPU-only. | GPU-only. |
| **Precision Options** | Highly granular (2-bit to 8-bit, including mixed K-quants). | Typically 4-bit. | 4-bit and 3-bit. |
| **Inference Engine** | `llama.cpp`, Ollama. | `vLLM`, Hugging Face TGI. | `vLLM`, AutoGPTQ. |
| **Concurrency Support** | Poor concurrent request handling on CPUs. | Excellent batching/concurrency on GPUs. | Moderate-to-good batching. |

**Selection Criteria:**
- **Choose GGUF** if deploying on machines without dedicated GPU VRAM (e.g., standard CPU servers or Mac Studio/Apple Silicon) or if you need to offload parts of a large model across CPU RAM and GPU VRAM.
- **Choose AWQ** if deploying on enterprise NVIDIA GPUs (e.g., A100, H100) and you need high-throughput concurrent inference. AWQ preserves model accuracy better than GPTQ for smaller models (e.g., 7B, 8B) because it protects active weights.
- **Choose GPTQ** if deploying on older GPUs or if AWQ kernels are unsupported, or if you need very fast token-generation speed for single-user workloads.

---

### Q2: Write the mathematical formula to estimate the VRAM required to load an LLM and run inference. Calculate the VRAM needed for Llama-3-8B in 4-bit precision with a context window of 8,192 tokens.
**Answer:**
To estimate the total VRAM required, we must account for both **Model Weights VRAM** and **KV Cache VRAM**.

#### 1. Formula:
$$\text{Total VRAM (GB)} = \left( \frac{N \times Q}{8} \times 1.2 \right) + \text{KV Cache VRAM (GB)}$$
- $N$ = Number of parameters (in billions).
- $Q$ = Quantization bits (e.g., 16 for FP16, 4 for 4-bit).
- $1.2$ = 20% overhead buffer for CUDA context, activations, and workspace memory.

#### 2. KV Cache VRAM Formula (per concurrent user request):
$$\text{KV Cache VRAM (Bytes)} = 2 \times L \times H \times D \times C \times B$$
- $2$ = Factor for Key and Value vectors.
- $L$ = Number of layers.
- $H$ = Number of Key-Value heads (depends on Grouped-Query Attention/GQA configuration).
- $D$ = Head dimension (hidden size / total heads).
- $C$ = Context window length (tokens).
- $B$ = Precision bytes (2 bytes for FP16).

#### 3. Calculation for Llama-3-8B (4-bit, 8K context):
- **Model weights:**
  $$\text{Weights VRAM} = \frac{8.03 \text{ billion} \times 4 \text{ bits}}{8} \times 1.2 \approx 4.82 \text{ GB}$$
- **KV Cache for Llama-3-8B:**
  - Layers ($L$) = 32
  - KV Heads ($H$) = 8 (uses GQA)
  - Head Dimension ($D$) = 128
  - Context ($C$) = 8192
  - Bytes ($B$) = 2 (FP16)
  $$\text{KV Cache VRAM} = 2 \times 32 \times 8 \times 128 \times 8192 \times 2 \approx 1,073,741,824 \text{ Bytes} \approx 1.00 \text{ GB}$$
- **Total VRAM Required:**
  $$\text{Total VRAM} \approx 4.82 \text{ GB} + 1.00 \text{ GB} = 5.82 \text{ GB}$$

*Note:* If serving multiple concurrent requests ($M$), the KV Cache VRAM scales linearly by $M$, unless KV cache sharing/paging is utilized.

---

### Q3: How does vLLM's PagedAttention resolve memory fragmentation in concurrent serving environments, and how does it compare to standard KV cache allocation?
**Answer:**
In standard LLM serving systems, the KV cache of a request must be stored in contiguous physical GPU memory. Because the output length of a request cannot be known in advance, the engine must pre-allocate contiguous space corresponding to the *maximum* possible context length (e.g., 8K or 32K tokens). 
This leads to two forms of waste:
1. **Internal Fragmentation:** Memory reserved for tokens that are never generated.
2. **External Fragmentation:** Unusable small gaps between concurrent requests.

**PagedAttention Solution:**
- Inspired by virtual memory in operating systems, **PagedAttention** partitions the KV cache of each request into fixed-size "pages" (e.g., containing KV vectors for 16 tokens).
- The physical pages do not need to be contiguous in GPU memory. The vLLM engine maintains a **Page Table** that maps logical token positions to non-contiguous physical pages.
- As new tokens are generated, the engine allocates free pages from a global page pool.

**Benefits:**
- **Zero Memory Waste:** Reduces VRAM waste from pre-allocations to less than 4% (only the last page of a request can have unused slots).
- **High Concurrency:** The freed VRAM is reclaimed by the engine to run larger batch sizes, increasing throughput by **2x to 4x** compared to standard Hugging Face Transformers serving.
- **Copy-on-Write (CoW) Sharing:** Allows multiple agent branches to share the same prefix KV cache (e.g., a long system prompt or prompt template) without duplicating memory, only copying pages when a branch writes new tokens.
