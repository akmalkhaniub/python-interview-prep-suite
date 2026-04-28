# 02: Azure AI Foundry and Services

The JD emphasizes Azure OpenAI and native services. Azure AI Foundry (formerly Azure AI Studio) is the central hub.

## 1. Azure OpenAI Service
*   **Models**: GPT-4o, GPT-4 Turbo, GPT-3.5-Turbo, and Embeddings (text-embedding-3-small/large).
*   **Provisioned Throughput (PTU)**: Guaranteed latency and throughput for high-scale apps, vs. Pay-as-you-go (TPM/RPM limits).
*   **Content Filtering**: Built-in safety layers to detect hate, violence, self-harm, and sexual content.

## 2. Azure AI Search (The Vector Store)
*   **Index Structure**: Fields for text, metadata, and vectors.
*   **Vector Search**: Supports HNSW and Exhaustive KNN.
*   **Semantic Ranker**: A secondary ranking pass using Microsoft's proprietary models to improve relevance.
*   **Indexers**: Automatic synchronization from data sources (Azure Blob Storage, SQL, Cosmos DB).

## 3. Azure AI Foundry (Core Orchestration)
*   **Prompt Flow**: A development tool to streamline the entire development cycle of AI applications (Develop -> Test -> Evaluate -> Deploy).
*   **Guardrails**: Implementing Content Safety and custom logic to prevent jailbreaking, PII leakage, and harmful content.
*   **Model Catalog**: Access to open-source models (Llama 3, Mistral, Phi-3) alongside Azure OpenAI.
*   **Experimentation**: Running batch evaluations to compare different prompts or models (e.g., GPT-4o vs GPT-3.5).

## 4. Cosmos DB for NoSQL
*   **Vector Search**: Ability to store and query vectors directly within Cosmos DB using DiskANN or HNSW.
*   **Integration**: Good for transactional data + AI features in the same database.

## Interview Questions
1.  **"Why would you use Azure AI Search instead of just a raw Chroma or Pinecone?"**
    *   *Ans*: Enterprise integration (VNETs, RBAC), Hybrid search capabilities, Semantic Ranker, and managed indexers for Azure data sources.
2.  **"Explain Prompt Flow in Azure AI Foundry."**
    *   *Ans*: It's a graph-based orchestration tool where you define nodes (LLM, Python code, Prompt) to build and test AI logic. It helps with observability and batch testing.
3.  **"How does Azure OpenAI handle data privacy?"**
    *   *Ans*: Customer data is NOT used to train the foundation models. Data stays within the Azure tenant and is encrypted.

## Practice Task
*   Draw a diagram of a RAG architecture using Azure AI Search as the retriever and Azure OpenAI as the generator, with Blob Storage as the document source.
