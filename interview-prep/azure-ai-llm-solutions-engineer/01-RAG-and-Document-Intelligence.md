# 01: RAG and Document Intelligence

This module focuses on the core responsibilities of building ingestion and retrieval pipelines for enterprise documents.

## 1. Document Ingestion & Parsing
Enterprise documents are rarely clean text. They include PDFs, Tables, and Images.

### Key Concepts
*   **SharePoint Ingestion**: Using `Microsoft Graph API` to pull documents. Understanding permissions (Delegated vs. Application) and handling delta syncs.
*   **Layout-Aware Parsing**: Using `Azure AI Document Intelligence` (formerly Form Recognizer) to identify headers, tables, and sections.
*   **Document Enrichment**: Adding metadata during ingestion (e.g., summary, detected language, entity extraction) to improve later retrieval.
*   **OCR**: Extracting text from scanned images.
*   **Chunking Strategies**:
    *   **Fixed-size**: Simple, but breaks context.
    *   **Recursive Character**: Breaks at paragraphs, sentences, and words to keep context together.
    *   **Semantic Chunking**: Using embeddings to find natural break points where meaning changes.
    *   **Layout-based**: Chunking by section or page.

## 2. Advanced RAG (Retrieval-Augmented Generation)
Simple RAG (Vector Search -> LLM) often fails in enterprise settings.

### Retrieval Optimization
*   **Hybrid Search**: Combining Keyword search (BM25) with Vector search to handle both exact matches and semantic meaning.
*   **Semantic Ranking**: Using a cross-encoder (like Azure AI Search Semantic Ranker) to re-rank the top K results for higher precision.
*   **Query Expansion/Rewriting**: Using an LLM to rewrite a user query into multiple search queries to cover more ground.

### Generation Optimization
*   **Context Window Management**: How to fit the most relevant info into the prompt without exceeding limits.
*   **Citation Handling**: Ensuring the LLM cites the specific document and page number used for the answer.

## 3. Interview Questions
1.  **"How would you handle a 500-page PDF with complex tables in a RAG system?"**
    *   *Ans*: Use Layout-aware parsing (Azure AI Document Intelligence). Convert tables to markdown or JSON. Use page-level metadata. Consider "Small-to-Big" retrieval (store small chunks for retrieval but provide larger context to LLM).
2.  **"What is the difference between a Vector DB and a Keyword search engine?"**
    *   *Ans*: Vector DBs use embeddings to find similarity in meaning. Keyword search (like BM25) finds exact text matches. For enterprise, Hybrid is usually best.
3.  **"How do you deal with 'lost in the middle' phenomenon in LLMs?"**
    *   *Ans*: Re-ranking helps by putting the most relevant context at the beginning or end of the prompt. Or use smaller, more focused context windows.

## Practice Task
*   Design a pipeline that extracts text from a multi-column PDF and stores it in a vector store with metadata for the source file and page number.
