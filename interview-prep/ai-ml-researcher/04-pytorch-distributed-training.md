# 04. Distributed Training with PyTorch

Researching novel architectures often requires training on hundreds or thousands of GPUs.

## 🚀 Distributed Strategies
- **DDP (Distributed Data Parallel):** Replicates the model on each GPU and synchronizes gradients. Simple, but the model must fit on a single GPU.
- **FSDP (Fully Sharded Data Parallel):** Shards model parameters, gradients, and optimizer states across GPUs. Allows for training models much larger than a single GPU's memory.
- **Pipeline Parallelism:** Splitting the model layers across different GPUs.
- **Tensor Parallelism:** Splitting individual tensors (weights) across GPUs (Megatron-LM style).

---

## 💾 Memory Optimization
- **Gradient Checkpointing:** Recomputes activations during the backward pass instead of storing them all. Saves memory at the cost of ~30% more compute.
- **Mixed Precision (AMP):** Using `BF16` (Brain Floating Point) or `FP16` for weights and gradients. `BF16` is preferred on modern hardware (A100/H100) as it has the same dynamic range as `FP32`.
- **Zero Redundancy Optimizer (ZeRO):** The technology behind DeepSpeed.
  - **ZeRO-1:** Shards optimizer states.
  - **ZeRO-2:** Shards gradients.
  - **ZeRO-3:** Shards parameters.

---

## 🛠️ The Research Training Loop (PyTorch)
A researcher should be comfortable writing custom loops for novel loss functions.

```python
import torch
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def train():
    # Setup distributed
    dist.init_process_group("nccl")
    rank = dist.get_rank()
    device = f"cuda:{rank}"
    
    model = MyNovelArchitecture().to(device)
    model = DDP(model, device_ids=[rank])
    
    optimizer = torch.optim.AdamW(model.parameters(), lr=1e-4)
    
    for batch in dataloader:
        optimizer.zero_grad()
        outputs = model(batch['input'].to(device))
        loss = my_custom_research_loss(outputs, batch['target'].to(device))
        loss.backward()
        optimizer.step()
```

---

## ❓ Research Interview Questions
1. **Why is BF16 preferred over FP16 for LLM training?**
   - *Answer:* FP16 has a very narrow range, leading to overflows/underflows that cause training instability. BF16 has the same range as FP32, making it much more robust for deep networks without needing complex loss scaling.
2. **Explain "All-Reduce" in distributed training.**
   - *Answer:* It's the communication primitive that sums gradients across all GPUs and broadcasts the result back to everyone, ensuring all models stay synchronized.
3. **How do you debug a "NaN" loss?**
   - *Answer:* Check for learning rate spikes, inspect gradients for explosions, verify that input data doesn't have NaNs, and consider adding gradient clipping.

---
**Next Step:** Learn how to document these findings in [05-research-technical-writing.md](05-research-technical-writing.md).
