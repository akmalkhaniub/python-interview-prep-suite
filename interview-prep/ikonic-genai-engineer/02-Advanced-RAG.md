# 02: Advanced RAG Systems

Retrieval-Augmented Generation (RAG) is the backbone of most LLM applications.

## 1. The RAG Pipeline
1. **Ingestion:** Loading documents (PDF, Docx, HTML).
2. **Chunking:** Splitting text into manageable pieces.
3. **Embedding:** Converting chunks into vectors using models like `text-embedding-3-small`.
4. **Storage:** Saving vectors in a database (Pinecone, FAISS).
5. **Retrieval:** Finding relevant chunks based on user query similarity.
6. **Generation:** Passing chunks + query to the LLM.

## 2. Chunking Strategies
- **Fixed-size:** Simple, but might cut sentences in half.
- **Recursive Character:** Splits by paragraphs, then sentences, etc.
- **Semantic Chunking:** Splits based on changes in meaning (using embeddings).
- **Parent-Document Retrieval:** Retrieve small chunks for better search, but pass the larger parent document to the LLM for context.

## 3. Improving Retrieval Quality
- **Hybrid Search:** `Embedding Similarity (Semantic) + BM25 (Keyword)`. Essential for finding specific terms or IDs.
- **Self-Querying:** The LLM rewrites the user query to include metadata filters (e.g., "Find papers from 2023").
- **Reranking (Cohere/BGE):** Retrieve 50 documents, then use a expensive "Reranker" model to pick the best 5.

## 4. Vector Databases
- **Pinecone:** Managed, scalable, cloud-native.
- **FAISS:** Facebook's library for local, ultra-fast similarity search.
- **Chroma/Weaviate:** Open-source alternatives with good LangChain integration.

## 5. Potential Interview Questions
1. **What is the "lost in the middle" problem?**
   - *Answer:* LLMs tend to ignore information placed in the middle of a very long context window. Solution: Retrieve fewer but more relevant chunks, or use reranking.
2. **How do you evaluate a RAG system?**
   - *Answer:* Use the **RAGAS** metrics:
     - **Faithfulness:** Is the answer based *only* on the context?
     - **Answer Relevance:** Does the answer address the question?
     - **Context Precision:** Are the retrieved chunks actually useful?
3. **Explain the difference between Symmetric and Asymmetric Semantic Search.**
   - *Answer:* **Symmetric:** Query and Document are similar in length/nature (e.g., finding similar sentences). **Asymmetric:** Short query, long document (standard RAG).
