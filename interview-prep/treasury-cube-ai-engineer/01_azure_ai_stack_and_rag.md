# Deep Dive: Azure AI Stack & Advanced RAG

For Treasury Cube, the RAG (Retrieval-Augmented Generation) pipeline must handle highly structured and sensitive financial data.

## 1. Azure AI Search: The Retrieval Engine

### Hybrid Search & Semantic Ranking
Treasury data often requires exact matches (e.g., account numbers, transaction IDs) combined with semantic understanding (e.g., "Why is my liquidity low in Germany?").
*   **Vector Search:** For conceptual queries.
*   **Keyword Search:** For exact financial identifiers.
*   **Semantic Ranker:** A secondary re-ranking layer that uses a cross-encoder to bubble up the most relevant chunks.

### Skillsets & Crackers
Use **Document Intelligence** as a skill within the AI Search indexer.
*   **OCR Skill:** To read scanned PDFs of bank statements.
*   **Custom Web API Skill:** To enrich data with exchange rates or internal treasury metadata during indexing.

## 2. RAG Evaluation with Azure AI Foundry

### The Evaluation Loop
1.  **Generate Test Dataset:** Use an LLM to generate 50 questions based on your treasury documentation and sample bank data.
2.  **Run Evaluation:** Use the `azure-ai-evaluation` SDK.
3.  **Metrics:**
    *   **Faithfulness:** Does the answer come *only* from the retrieved bank data? (Crucial for CFO trust).
    *   **Context Recall:** Did the search find all the relevant transactions needed to answer the question?

## 3. Implementing Tool-Calling in .NET/Python

Treasury Cube uses AI to *do* things, not just talk.
*   **Function Calling:** Define a tool `get_cash_position(bank: string, currency: string)`.
*   **Structured Outputs:** Use `response_format: { type: "json_schema", ... }` to ensure the model returns a schema that matches your treasury dashboard.

## 4. Multi-Tenant Search
*   **Security Filters:** Ensure every query to AI Search includes a filter on `organizationId`.
*   **Index-per-Tenant vs. Shared Index:** 
    *   *Shared Index:* Use metadata filters (more cost-effective).
    *   *Index-per-Tenant:* Higher isolation (more expensive).

## Interview Questions
1.  "How do you handle 'chunking' for a complex financial table in a bank statement so the LLM doesn't lose the row/column context?"
2.  "Why would you choose Azure AI Search over a simple Pinecone or ChromaDB for an enterprise Treasury app?"
3.  "Describe your strategy for preventing the model from hallucinating a balance that doesn't exist."
