# 03 - Local AI: Llama.cpp & GGML

## Inference Engines

### 1. What is Llama.cpp and why is it revolutionary?
**Answer:**
A C++ implementation of Meta's Llama models (and others) designed for local execution.
- **Why:** It allows high-performance inference on consumer hardware (MacBooks, Windows PCs) by using **4-bit quantization** and leveraging CPU/GPU (Metal, CUDA) efficiently. It eliminates the need for expensive cloud GPUs for many use cases.

### 2. What is the GGML / GGUF format?
**Answer:**
- **GGML:** The original tensor library for machine learning on the CPU.
- **GGUF:** The newer, standard file format for models used by Llama.cpp. It stores weights and metadata in a single, extensible file. It is designed to be backwards compatible and supports different architectures.

### 3. Explain "Quantization" (e.g., Q4_K_M).
**Answer:**
The process of reducing the precision of model weights (e.g., from 16-bit floats to 4-bit integers). 
- **Benefit:** Significantly reduces the memory (RAM) requirement and speeds up inference.
- **Tradeoff:** Slight loss in model accuracy/perplexity, but usually negligible for 4-bit and above.

## Local Deployment

### 4. Benefits of Local AI vs. Cloud AI for a platform like QVAC?
**Answer:**
- **Privacy:** Data never leaves the device. Essential for sensitive finance or personal data.
- **Cost:** No per-token or hourly server fees.
- **Offline Capability:** Works without an internet connection.
- **Latency:** Zero network latency (though inference time depends on local hardware).

### 5. How do you handle "Hardware Heterogeneity" in DevRel?
**Answer:**
As an advocate, you must provide guides for:
- **Apple Silicon:** Optimizing for Metal.
- **NVIDIA GPUs:** Optimizing for CUDA.
- **CPU-only:** Optimizing for AVX/AVX2.
The code should be portable, or the SDK should abstract the hardware selection.

### 6. What are the limitations of local inference?
**Answer:**
- **Limited Context:** Hard to fit massive context windows (e.g., 1M tokens) into consumer RAM.
- **Speed:** Slower than a cluster of A100s/H100s for large batches.
- **Model Size:** You can't realistically run a 400B parameter model on a laptop.
- **Power Consumption:** Inference is battery-intensive for mobile/laptops.
