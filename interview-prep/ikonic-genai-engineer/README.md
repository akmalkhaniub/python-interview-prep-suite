# Interview Prep: Generative AI Engineer @ IKONIC

IKONIC is looking for a specialist in **Agentic AI** and **RAG**. This role requires a balance between modern LLM orchestration and foundational Deep Learning knowledge.

## Preparation Roadmap

### 1. Agentic AI & Orchestration
- [ ] **LangGraph:** Building cyclic graphs for agents, state management, and "Human-in-the-loop" patterns.
- [ ] **Tool Use:** Implementing function calling for agents to interact with external APIs/Databases.
- [ ] **Memory:** Conversational memory vs. Long-term memory (Vector DBs).

### 2. Advanced RAG (Retrieval-Augmented Generation)
- [ ] **Chunking & Embedding:** Choosing the right strategy (Parent Document, Semantic Chunking).
- [ ] **Vector Databases:** Indexing and querying in Pinecone/FAISS.
- [ ] **Hybrid Search:** Combining semantic search with keyword search (BM25).
- [ ] **Reranking:** Using Cross-Encoders to improve retrieval quality.

### 3. Foundational AI/ML
- [ ] **Transformers:** Self-attention mechanism, Multi-head attention, Positional encoding.
- [ ] **Legacy Architectures:** RNNs, LSTMs (Why they were replaced by Transformers).
- [ ] **CNNs:** Basics of convolutional layers (mentioned in JD).

### 4. Fine-tuning & Evaluation
- [ ] **LoRA & QLoRA:** Parameter-efficient fine-tuning (PEFT).
- [ ] **Evaluation Frameworks:** RAGAS (for RAG), G-Eval, or custom LLM-as-a-judge.

### 5. Production & Deployment
- [ ] **API Design:** Building FastAPI wrappers for LLM services.
- [ ] **MLOps:** Dockerizing AI pipelines and deploying to Cloud (AWS/GCP).

## Study Material (Notebooks)
1. [Agentic AI & LangGraph](./01-Agentic-AI.md)
2. [Advanced RAG Systems](./02-Advanced-RAG.md)
3. [Transformers & Deep Learning Fundamentals](./03-Transformers-Fundamentals.md)
4. [Fine-tuning & Model Evaluation](./04-FineTuning-Evaluation.md)
5. [Production AI Pipelines](./05-Production-AI.md)

---

## Key Interview Focus
- **LangGraph vs LangChain:** Understand when to use a graph-based approach (cyclic) vs. a chain-based approach (linear).
- **Tool Calling:** How do you handle agents failing to call a tool or providing wrong arguments?
- **US Shift:** Be prepared for the 4pm-1am timings.
