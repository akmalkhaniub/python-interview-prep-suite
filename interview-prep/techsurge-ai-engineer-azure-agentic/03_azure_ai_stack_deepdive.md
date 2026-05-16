# Deep Dive: Azure AI Stack for Agentic Systems

Techsurge specifically requires experience with the Azure stack. This guide maps your existing knowledge to the Azure ecosystem for the Techsurge role.

---

## 1. Azure AI Document Intelligence (Multi-Modal Ingestion)
*   **What it is:** A service that uses OCR and vision to extract structure from documents (PDFs, forms, ID cards, receipts).
*   **Agentic Use Case:** Feeding a "Multi-Modal RAG" pipeline. Instead of just raw text, you get key-value pairs, tables, and layout information.
*   **Talking Point:** "I prefer using specialized OCR services like Document Intelligence before the LLM step to ensure structural integrity of data, especially for complex tables in medical or financial forms."

## 2. Azure AI Search (Hybrid/Vector Retrieval)
*   **What it is:** The backbone of RAG on Azure. Supports Vector search (Embeddings), Keyword search (BM25), and **Hybrid Search** with **Semantic Ranker**.
*   **Key Feature:** **Hybrid Search + Re-ranking**. This is how you achieve high "Grounding Accuracy" mentioned in the JD.
*   **Talking Point:** "For production RAG, simple vector similarity isn't enough. I implement Hybrid Search in Azure AI Search to combine the precision of keyword matching with the conceptual understanding of embeddings, followed by a Semantic Re-ranking step."

## 3. Azure OpenAI Service & API Management (APIM)
*   **What it is:** Managed OpenAI models with enterprise-grade security (Entra ID), private networking, and **Content Safety**.
*   **Production Control:** Use **Azure API Management (APIM)** for:
    *   **Rate Limiting:** Throttling tokens/requests per user/app.
    *   **Circuit Breakers:** Failing fast if the LLM endpoint is down or latent.
    *   **Load Balancing:** Distributing requests across multiple Azure OpenAI regions to avoid PTU (Provisioned Throughput) limits.
*   **Talking Point:** "I leverage Azure APIM as an AI Gateway to enforce token budgets and implement circuit breakers, ensuring that a single malfunctioning agent doesn't exhaust the enterprise quota."

## 4. Application Insights & Azure Monitor (AgentOps)
*   **What it is:** The "Observability" requirement in the JD.
*   **Agentic Use Case:** Tracing "Spans."
    *   **Span 1:** User Request Received.
    *   **Span 2:** Intent Routing (Planner Agent).
    *   **Span 3:** Retrieval from AI Search.
    *   **Span 4:** Tool Execution (Internal API).
    *   **Span 5:** Final Answer Synthesis.
*   **Talking Point:** "I use Application Insights to trace every node in a LangGraph workflow. This allows me to calculate 'Time to First Token' (TTFT) for each agent and identify if a specific tool call is causing a bottleneck."

## 5. Security & Identity (Entra ID & Key Vault)
*   **Entra ID (formerly Azure AD):** Managing identity for the agent itself (Managed Identity) so it can securely access AI Search or Key Vault without hardcoded keys.
*   **Key Vault:** Storing API keys, secrets, and connection strings.
*   **Talking Point:** "Security is a first-class citizen in my agent designs. I use Managed Identities for service-to-service authentication, ensuring that the AI agent never has direct access to raw credentials."
