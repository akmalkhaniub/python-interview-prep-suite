# 03 - Large-Scale Distributed Training

## Parallelism Strategies

### 1. Data Parallelism (DDP) vs. Model Parallelism?
**Answer:**
- **DDP (Distributed Data Parallel):** Each GPU has a copy of the model but processes different data batches. Gradients are averaged at the end.
- **Model Parallelism:** The model itself is split across GPUs. 
    - **Tensor Parallelism (TP):** Splitting layers (e.g., Attention heads) across GPUs.
    - **Pipeline Parallelism (PP):** Splitting different layers across GPUs.

### 2. What is ZeRO (Zero Redundancy Optimizer)?
**Answer:**
A technique (from Microsoft/DeepSpeed) to eliminate memory redundancy in distributed training by partitioning optimizer states, gradients, and parameters across GPUs.
- **ZeRO-3:** Offloads almost everything, allowing training of massive models that wouldn't fit on a single node.

### 3. Difference between FP16, BF16, and FP8?
**Answer:**
- **FP16:** 16-bit float. Requires loss scaling to prevent underflow.
- **BF16 (Bfloat16):** 16-bit float with the same dynamic range as FP32. More stable for training than FP16.
- **FP8:** 8-bit float. Used for massive speedups in inference and, increasingly, in training (H100 GPUs).

## Infrastructure & Tools

### 4. What is "Megatron-LM" and "DeepSpeed"?
**Answer:**
- **Megatron-LM:** NVIDIA's framework for training massive transformers using TP and PP.
- **DeepSpeed:** Microsoft's library for optimizing training speed and scale (ZeRO, 3D Parallelism).

### 5. How do you handle "Node Failures" during a 3-month training run?
**Answer:**
- **Checkpointing:** Regularly saving model state to persistent storage.
- **Fault-tolerant clusters:** Using orchestrators that automatically restart failed nodes and resume from the latest checkpoint.

### 6. Importance of "Interconnect" (NVLink/InfiniBand)?
**Answer:**
Distributed training requires massive bandwidth between GPUs. NVLink is for intra-node (same machine) and InfiniBand is for inter-node (across machines) communication.
