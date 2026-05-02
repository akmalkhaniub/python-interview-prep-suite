# 02 - RAG & Vector Databases

## Retrieval-Augmented Generation (RAG)

### 1. Explain the end-to-end RAG pipeline.
**Answer:**
1. **Ingest:** Load documents (PDF, Text, DB).
2. **Chunk:** Split documents into smaller pieces.
3. **Embed:** Convert chunks into vectors using an embedding model (e.g., `text-embedding-3-small`).
4. **Store:** Save vectors in a Vector DB (e.g., Pinecone).
5. **Retrieve:** When a query comes, embed it, and find the top-K similar chunks in the DB.
6. **Generate:** Pass the chunks + query to the LLM to generate the final answer.

### 2. What are common "Chunking" strategies?
**Answer:**
- **Fixed-size:** e.g., 500 characters. Simple but can cut off sentences.
- **Recursive Character:** Splits by headers, then paragraphs, then sentences.
- **Semantic Chunking:** Uses embeddings to split where the meaning changes.

### 3. Difference between "Dense" and "Sparse" Retrieval?
**Answer:**
- **Dense:** Uses embeddings (vectors) to find semantic similarity. Good for "meaning."
- **Sparse:** Uses keyword matching (BM25/TF-IDF). Good for specific names, serial numbers, or rare terms.
- **Hybrid Search:** Combines both for the best results.

## Vector Databases

### 4. What is HNSW (Hierarchical Navigable Small World)?
**Answer:**
An algorithm for **Approximate Nearest Neighbor (ANN)** search. It builds a multi-layered graph where the top layers have fewer nodes (long distances) and bottom layers have more nodes (short distances). It allows for sub-linear search time in high-dimensional spaces.

### 5. How do you handle "Stale Data" in a Vector DB?
**Answer:**
- **Upserting:** Updating existing vectors when the source document changes.
- **Metadata Filtering:** Tagging vectors with versions or timestamps and filtering during retrieval.
- **Namespacing:** Partitioning the DB for different users or document types.

### 6. What is "Re-ranking" and why use it?
**Answer:**
Retrieval might return 50 chunks, some of which are irrelevant. A **Re-ranker** (like Cohere Rerank) is a smaller, slower, but more accurate model that scores the top chunks against the query. You then send only the top 5 re-ranked chunks to the LLM.
