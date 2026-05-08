# Technical Study Guide: AI Engineer (Treasury Cube)

## 1. Azure AI Stack Deep Dive
- **Azure AI Search:** 
    - Master **Hybrid Search** (Keyword + Vector).
    - Understand **Semantic Ranking**.
    - Indexing complex financial documents with **Skillsets**.
- **Azure AI Document Intelligence:** 
    - Custom extraction models for bank statements (CAMT.053).
    - Handling tables and multi-page financial reports.
- **Azure AI Foundry (formerly AI Studio):**
    - Prompt Flow for orchestrating RAG.
    - Model catalog and deployment strategies.
- **Azure Content Safety:**
    - Setting up filters for PII and sensitive financial data.

## 2. RAG & Agentic Patterns
- **Semantic Kernel (SK):**
    - Skills, Plugins, and Planners.
    - Integrating SK with .NET/C# for enterprise workflows.
- **Structured Outputs:**
    - Using JSON mode and function calling to ensure the agent returns valid treasury data schemas.
- **Evaluation Frameworks:**
    - Using **Azure AI Evaluation SDK**.
    - RAG metrics: Faithfulness, Answer Relevance, Context Precision.

## 3. Treasury & FinTech Domain
- **ISO 20022:**
    - **CAMT:** Cash Management (e.g., CAMT.053 for statements).
    - **PAIN:** Payments Initiation (e.g., PAIN.001).
- **Cash Positioning:** Calculating daily liquidity across multiple banks and currencies.
- **FX Exposure:** Understanding how currency fluctuations affect corporate balances.

## 4. Azure Infrastructure for AI
- **Durable Functions:** Implementing the "Agent" state machine for long-running investigations.
- **Logic Apps:** Connecting AI to legacy banking systems or ERPs.
- **Data Isolation:** Azure SQL Row-Level Security (RLS) in a multi-tenant setup.

## 5. LLMOps & Observability
- **Application Insights:** Tracking custom AI metrics (token count, prompt ID).
- **Prompt Versioning:** Using Prompt Flow versions or custom git-based workflows.
- **Hallucination Detection:** Implementing secondary "checker" prompts for high-stakes financial data.

## Resources to Review
- [Azure AI Search: Vector Search Documentation](https://learn.microsoft.com/en-us/azure/search/vector-search-overview)
- [Semantic Kernel Documentation](https://learn.microsoft.com/en-us/semantic-kernel/)
- [ISO 20022 for Dummies (Basics)](https://www.iso20022.org/)
- [Azure AI Document Intelligence: Prebuilt Models](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/concept-model-overview)
