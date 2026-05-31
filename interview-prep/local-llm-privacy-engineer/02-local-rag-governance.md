# Module 02: Local RAG & Compliance

This module covers the architecture of secure, on-premises Retrieval-Augmented Generation (RAG) pipelines, metadata-level access controls, and network leakage auditing in air-gapped environments.

---

## Technical Q&A

### Q1: Design a completely local, high-performance RAG indexing and retrieval pipeline. What models and databases would you run on-premises?
**Answer:**
A robust, local RAG stack must run without outbound internet access:

1. **Local Embedding Model:**
   - **Model:** `nomic-embed-text` or `BAAI/bge-large-en-v1.5` (loaded via Python `sentence-transformers` or served locally via Ollama).
   - **Why:** These models are compact (approx. 300M to 1GB VRAM footprint) and rank at the top of the MTEB (Massive Text Embedding Benchmark) leaderboard, matching or beating commercial cloud embeddings.

2. **Self-Hosted Vector Database:**
   - **Database:** **Qdrant** or **PostgreSQL with pgvector** run as local Docker containers.
   - **Why:** Qdrant is written in Rust, offers high-speed indexing, and supports payload filtering and local hybrid search. `pgvector` is excellent if the enterprise already relies on Postgres, maintaining transaction safety and backup logic natively.

3. **Local Re-ranking Model:**
   - **Model:** `BAAI/bge-reranker-large`.
   - **Why:** Retrieval by cosine similarity alone (first-stage retrieval) can fetch irrelevant contexts due to vocabulary differences. Running a small local cross-encoder model (re-ranker) over the top 25 retrieved results dramatically increases precision by evaluating the full relationship between the question and the documents.

---

### Q2: How do you enforce document-level and group-level authorization filters in a RAG database so that the LLM is only fed context the user is permitted to see?
**Answer:**
Enforcing security rules directly in the LLM prompt (e.g., "Do not tell the user about documents they don't have access to") is insecure due to prompt injection risks. Authorization **must be enforced at the database level** during the retrieval phase.

**Implementation Pattern (e.g., using pgvector/Postgres):**
1. **Metadata Tagging:** When indexing documents, include an access control list (ACL) as payload metadata:
   ```json
   {
     "document_id": "doc_1029",
     "content": "Confidential financial projections...",
     "allowed_roles": ["finance_admin", "executive_board"],
     "owner_id": "user_8912"
   }
   ```
2. **Filtered Query Execution:** When the user queries the system, resolve the user's role and user ID from the authenticated request session. Run the vector similarity query with strict metadata filter predicates:
   ```sql
   -- SQL query combining semantic search and access control filters
   SELECT document_id, content, (embedding <=> :user_query_vector) AS distance
   FROM document_embeddings
   WHERE (allowed_roles && :user_roles OR owner_id = :authenticated_user_id)
   ORDER BY distance ASC
   LIMIT 5;
   ```
3. **Outcome:** The database engine filters out unauthorized records before performing the vector distance calculation, ensuring that unauthorized data is never loaded into the LLM's context window.

---

### Q3: How do you audit and verify that a locally deployed LLM server (such as Ollama or vLLM) does not leak telemetry data or prompt histories to external cloud endpoints?
**Answer:**
To verify that an AI application is fully air-gapped, perform the following verification audits:

1. **Environment Variables Config:**
   - Disable built-in telemetry parameters:
     - For Hugging Face: Set `HF_HUB_DISABLE_TELEMETRY=1`.
     - For vLLM: Set `VLLM_NO_USAGE_STATS=1`.
     - For Ollama: Block network outbound or run with `--host 127.0.0.1`.

2. **Outbound Socket Auditing (using PowerShell on Windows):**
   - Monitor the PID of the model server (e.g., `ollama_llama_server.exe` or `python.exe` running vLLM) to verify no active connections are established to external IPs:
     ```powershell
     # Find active TCP connections for the target process
     Get-NetTCPConnection -State Established | Where-Object { 
         $_.OwningProcess -eq (Get-Process -Name "ollama").Id 
     }
     ```

3. **Local Docker Network Isolation:**
   - Run the entire AI stack inside a Docker network that lacks a default gateway (isolated bridge):
     ```yaml
     # docker-compose.yml snippet
     networks:
       private_ai:
         internal: true # Disables external internet gateway access
     ```
   - This physically prevents any process on the network from resolving DNS or sending HTTP packets to external endpoints, while allowing local microservices (backend API, database, LLM server) to communicate.
