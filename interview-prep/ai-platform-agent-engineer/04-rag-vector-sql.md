# 04. Advanced Retrieval: Vector Search & Text-to-SQL

This guide covers the "Knowledge" layer of the agent platform, focusing on **Pinecone** and **Vanna AI**.

## 🔍 Vector Search (Pinecone)
Production RAG is more than just `vector_store.search()`.

### 1. Retrieval Pipelines
- **Multi-Query Retrieval:** Using an LLM to rewrite a user query into 3-5 variations to capture different semantic nuances.
- **Reranking:** Fetching top 50 results from Pinecone, then using a **Cohere Reranker** or similar model to pick the top 5 that actually matter.
- **Hybrid Search:** Combining vector similarity (semantic) with BM25 (keyword) search for better accuracy on specific terms like product IDs.

### 2. Strategy & Evaluation
- **Chunking:** Semantic chunking vs. Recursive Character splitting.
- **Evaluation:** Using metrics like **Faithfulness** and **Answer Relevance** (from the RAGAS framework).

## 📊 Text-to-SQL (Vanna AI)
The JD mentions building text-to-SQL systems over real production databases.

### 1. How Vanna AI Works
Vanna is an LLM-based Python framework for SQL generation.
- **Schema Training:** You "train" Vanna by providing DDL statements, documentation, and a set of "Golden SQL" queries.
- **Query Validation:** Before executing generated SQL, the system must check for safety (e.g., no `DROP TABLE`) and correctness (parsing the query plan).
- **Safety Controls:** Using Read-Only RDS users and row-level security.

### 2. The Text-to-SQL Pipeline
```text
User Question -> [Vanna / Agent] -> SQL Generation -> [Query Plan Validation] -> [Execute on RDS] -> Result -> [Summarization] -> User
```

## ❓ Common Interview Questions
1. **How do you prevent a Text-to-SQL agent from deleting data?**
   - *Answer:* Use a read-only database user at the IAM/RDS level. Add a regex/LLM classifier guardrail to detect "destructive" intents before execution.
2. **What is "Semantic Chunking"?**
   - *Answer:* It's a method where chunks are broken based on semantic similarity rather than a fixed character count. You use a model to detect when the "topic" changes and split there.
3. **Why use Pinecone over a local vector store like FAISS in production?**
   - *Answer:* Scalability, managed infrastructure, real-time index updates, and metadata filtering capabilities that are easier to manage at scale.

---
**Next Step:** Learn how to ensure quality and safety in [05-evals-guardrails.md](05-evals-guardrails.md).
