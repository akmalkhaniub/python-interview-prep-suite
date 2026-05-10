# Technical Study Guide: Data Scientist (LineSlip Solutions)

## 1. LLM Fine-Tuning (LoRA & QLoRA)
- **PEFT (Parameter-Efficient Fine-Tuning):** Understanding how LoRA injects low-rank matrices to update weights without full retraining.
- **Quantization:** BitsAndBytes (4-bit, 8-bit) and its role in QLoRA for fitting large models on consumer or mid-tier enterprise GPUs.
- **Unsloth:** Mastering the Unsloth library for 2x faster fine-tuning and 0% accuracy loss.
- **Training Loops:** Hugging Face `SFTTrainer` and `DPO` (Direct Preference Optimization).

## 2. RAG & Vector Databases
- **Chunking Strategies:** Semantic chunking vs. fixed-size chunking for insurance binders.
- **Embeddings:** Fine-tuning SentenceTransformers on domain-specific insurance data.
- **Hybrid Search:** Combining keyword search (BM25) with vector search for maximum retrieval accuracy.
- **Reranking:** Using Cohere or local Cross-Encoders to improve RAG precision.

## 3. Agentic AI (LangChain & LangSmith)
- **LangChain Expression Language (LCEL):** Building complex, readable chains.
- **LangSmith:** Tracing, evaluating, and monitoring agent performance in production.
- **Autonomous Agents:** Using `Plan-and-Execute` or `Self-Reflect` patterns for multi-document extraction.

## 4. Azure Cloud & MLOps
- **Azure Machine Learning (AML):** Creating workspace, managed compute, and endpoints for LLMs.
- **AKS (Azure Kubernetes Service):** Deploying containerized LLM inference engines (e.g., vLLM or TGI).
- **MLflow:** Logging experiments, metrics (Perplexity, F1), and model versioning.
- **Distributed Training:** Using **DeepSpeed** for Zero-Redundancy Optimizer (ZeRO) stages.

## 5. Large Context Windows
- **Architectures:** Sliding window attention, Flash Attention 2, and specialized models like **Qwen-7B-Instruct-Long** or **BigBird**.
- **The "Lost in the Middle" Problem:** Strategies to ensure models don't ignore information in the middle of long documents.

## 6. Linux & DevOps
- **Shell Scripting:** Automating training logs, data preprocessing, and container management.
- **Docker:** Multi-stage builds and optimizing image sizes for heavy GPU-based workloads.

## Resources to Review
- [Unsloth AI Documentation](https://unsloth.ai/docs/)
- [Hugging Face PEFT Guide](https://huggingface.co/docs/peft/index)
- [LangSmith: Evaluation Cookbook](https://docs.smith.langchain.com/)
- [DeepSpeed: Training Large Models](https://www.deepspeed.ai/)
- [Pinecone: RAG Best Practices](https://www.pinecone.io/learn/retrieval-augmented-generation/)
