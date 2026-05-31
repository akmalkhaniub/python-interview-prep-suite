# Local LLM & Privacy-First AI Engineer

## About the job
**Location: Zurich, Switzerland / Remote**

### About the Company
We are a Swiss-based enterprise software firm serving clients in healthcare, banking, and government sectors. Due to strict data residency, GDPR, and HIPAA compliance requirements, our clients cannot send proprietary data to public cloud APIs. We specialize in building and deploying **private, air-gapped, on-premises AI solutions** utilizing highly optimized, open-weights large language models.

### Role Overview
We are looking for a **Local LLM & Privacy-First AI Engineer** to lead the deployment, optimization, and orchestration of local model inferences. In this role, you will select, fine-tune, and deploy models (e.g., Llama 3, Mistral, Qwen) on local server clusters, workstations, and edge hardware. You will design privacy-first RAG architectures, manage local GPU resource scheduling, and ensure all AI interactions happen strictly within client firewall boundaries.

### Key Responsibilities
- **Model Deployment & Serving:** Deploy open-weights models locally using high-performance serving libraries like vLLM, Ollama, or llama.cpp.
- **Model Optimization:** Apply post-training quantization techniques (e.g., GGUF, GPTQ, AWQ) to compress models to fit within restricted GPU VRAM configurations while preserving reasoning accuracy.
- **On-Premises Infrastructure:** Design and maintain local GPU resource pools (NVIDIA A100/H100, Mac Studios) and configure containerized model orchestrations via Docker/Kubernetes.
- **Private Retrieval (RAG):** Design local vector search pipelines using secure, self-hosted databases (Chroma, pgvector, Milvus) and local embedding models.
- **Data Sovereignty Compliance:** Audit data flows and verify that no telemetry, logging, or input parameters escape the air-gapped environment.

### Must-Have Qualifications
- **ML / Infrastructure Engineering:** 5+ years of software engineering experience, with 2+ years deploying models in production environments.
- **Local Serving Experience:** Deep familiarity with vLLM, Ollama, or llama.cpp, including context window configuration and GPU memory tuning.
- **Quantization Expertise:** Hands-on experience with quantization frameworks (AutoAWQ, llama.cpp quantize tools) and memory-performance profiling.
- **Infrastructure Competency:** Practical experience managing Docker containers, Kubernetes clusters, and local GPU configurations (CUDA, ROCm, Apple Metal).
- **Strong Language Fundamentals:** Expert-level scripting and application development in **Python** and bash.

### Nice-to-Have Qualifications
- Experience with parameter-efficient fine-tuning (PEFT) techniques like LoRA/QLoRA on local hardware.
- Strong knowledge of data compliance frameworks (GDPR, HIPAA, SOC2) and security hardening.
- Contribution to open-source model serving repositories or quantization projects.
