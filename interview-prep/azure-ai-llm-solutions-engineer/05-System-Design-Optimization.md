# 05: System Design and Optimization

Scaling AI solutions for enterprise-grade performance and cost.

## 1. Cost Optimization
*   **Token Management**: Truncating irrelevant context, using smaller models (e.g., GPT-3.5 or Phi-3) for simple tasks like classification, and caching frequent queries.
*   **Semantic Caching**: Storing (Query, Answer) pairs in a vector database. If a new query is semantically similar to a cached one, return the cached answer instead of calling the LLM.

## 2. Performance & Latency
*   **Parallelization**: Running multiple retrieval or extraction steps in parallel.
*   **Streaming**: Reducing "Time to First Token" (TTFT) for better user experience.
*   **Load Balancing**: Distributing requests across multiple Azure OpenAI deployments or regions to avoid rate limits.

## 3. Orchestration & Pipeline Scalability
*   **Azure Data Factory (ADF)**: Orchestrating complex ingestion workflows (e.g., from SharePoint to Blob Storage to AI Search).
*   **Azure Functions**: Serverless compute for lightweight tasks like document classification or triggering an embedding generation when a file is uploaded.
*   **Queue-based Processing**: Using Azure Service Bus or Storage Queues to handle ingestion spikes and ensure reliability.

## 4. Governance, Security & Compliance
*   **RBAC (Role-Based Access Control)**: Using Azure AD (Entra ID) to restrict access to model deployments and data indexes.
*   **Security Trimming**: Ensuring users only retrieve chunks from documents they have permission to see in the source system (e.g., SharePoint permissions).
*   **Data Residency**: Configuring Azure resources in specific regions to comply with local data laws.
*   **Audit Logging**: Using Diagnostic Settings to track who accessed which model and what was requested.

## Interview Questions
1.  **"A customer is complaining that their RAG system is too slow. Where do you start looking?"**
    *   *Ans*: Check the trace. Is it the ingestion (rarely)? The retrieval (vector search latency)? The LLM generation (usually the bottleneck)? I'd implement streaming and check the number of tokens being sent.
2.  **"How do you ensure your AI system doesn't leak sensitive data from one department to another?"**
    *   *Ans*: Implement "Security Trimming" or "Access Control Filters" in the vector store. Each document should have metadata tags for permissions, and the search query should include a filter for the current user's groups.
3.  **"What is 'Semantic Caching' and when would you use it?"**
    *   *Ans*: It's caching responses based on the meaning of the query rather than exact text. Use it for high-volume, repetitive queries (e.g., "What is the holiday policy?") to save costs and reduce latency.

## Practice Task
*   Design a high-level architecture for a multi-tenant chatbot that ensures data isolation between different clients.
