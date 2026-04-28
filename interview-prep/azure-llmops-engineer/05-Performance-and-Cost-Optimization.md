# 05: Performance and Cost Optimization

Tuning the system for the best balance of quality, speed, and budget.

## 1. RAG Hyperparameter Tuning
*   **Chunk Size**: Balancing between too small (loss of context) and too large (too much noise, high token cost).
*   **Retrieval Depth (K)**: Number of documents retrieved. Increasing K improves quality but increases LLM cost and latency.
*   **Hybrid Search Weights**: Finding the optimal balance between keyword search (BM25) and vector search for your specific domain.

## 2. Cost-Saving Strategies
*   **Semantic Caching**: Caching common queries and their responses in a vector database (e.g., Redis or Cosmos DB) to avoid redundant LLM calls.
*   **Model Selection**: Routing simpler tasks (Classification, Summarization) to cheaper models like GPT-3.5 or Phi-3, and reserving GPT-4 for complex reasoning.
*   **Prompt Compression**: Using tools to remove redundant tokens from the prompt without losing meaning.

## 3. Scaling Strategies
*   **Autoscaling**: Setting up scale rules for your API layer (App Service/AKS) based on CPU/Memory or Request Queue depth.
*   **Provisioned Throughput (PTU)**: Switching from pay-as-you-go to PTUs in Azure OpenAI for high-volume applications to ensure consistent latency and cost predictability.

## Interview Questions
1.  **"How would you optimize the chunk size for a RAG system containing legal documents?"**
    *   *Ans*: I would perform a 'Search Quality Experiment'. I'd test various chunk sizes (500, 1000, 2000 tokens) against a set of 'Golden Q&A' pairs and measure Context Precision and Recall to find the sweet spot.
2.  **"What is 'Semantic Caching' and how does it save money?"**
    *   *Ans*: It caches the results of similar queries. If a user asks "What is the vacation policy?" and it was recently answered, we return the cached answer instead of calling the LLM again, saving tokens and time.
3.  **"How do you handle rate limits (429 errors) in a high-traffic LLM application?"**
    *   *Ans*: I implement exponential backoff and retry logic. I also use multiple Azure OpenAI deployments in different regions and load-balance between them to distribute the TPM load.

## Practice Task
*   Design a logic for a 'Model Router' that sends simple questions to GPT-3.5 and complex ones to GPT-4o.
