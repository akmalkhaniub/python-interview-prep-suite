# Interview Questions & Expert Answers: Full-Stack AI Engineer (Deloitte Luxembourg)

This document contains targeted technical interview questions and high-level expert answers designed for the Full-Stack AI Engineer role at Deloitte Luxembourg. It covers hybrid enterprise architectures (Spring Boot/Python), document parsing, secure RAG (RBAC), and European AI compliance (EU AI Act and GDPR).

---

## Part 1: Enterprise Architecture & Document Processing

### 1. Why is a hybrid Spring Boot (Java) and FastAPI (Python) architecture commonly used in European financial institutions? How do you secure data transit between them?
**Expert Answer:**
"This hybrid design separates enterprise governance from AI execution:
- **Spring Boot (Java):** Used for the outer core. It handles OAuth2/OIDC authentication, integrates with active directory (LDAP), manages enterprise databases (Oracle/SQL Server) with ACID transactions, and enforces strict security audits. Java's static typing and maturity make it the preferred runtime for compliance-focused financial backends.
- **FastAPI (Python):** Used for the inner AI core. It loads libraries like PyTorch, tokenizes inputs, interfaces with Azure OpenAI, and queries local vector databases.
- **Secure Transit:**
  1. **gRPC over mTLS:** For low-latency communication, I use gRPC. I enforce Mutual TLS (mTLS) where both the Java client and Python server authenticate each other using certificates managed by HashiCorp Vault.
  2. **Token Relay:** The Spring Boot gateway validates the incoming JWT from the client and relays a sanitized, scoped service token (with strict RBAC claims) to the Python FastAPI node, ensuring the Python layer knows the user's identities and permissions."

### 2. Financial institutions require RAG over complex PDFs containing tables, diagrams, and footnotes. How do you parse and chunk these documents to maintain structural context?
**Expert Answer:**
"Standard text splitters fail on financial documents because they flatten tables into meaningless strings. I use a multi-modal/layout-aware parsing approach:
1. **Layout Detection:** I use tools like **LayoutParser** or **Unstructured.io** (or Azure Document Intelligence) to segment the PDF into semantic blocks (headings, paragraphs, tables, images).
2. **Table Processing:** I do not convert tables to raw text. Instead:
   - I extract tables as HTML tables or JSON structures, which preserve column/row relationships.
   - I pass table structures to a small LLM (like GPT-4o-mini) to generate a text summary (e.g., 'This table shows Q4 revenue breakdown by country...').
   - I index the table summary in the vector database, but when a match is found, I return the original HTML table structure to the LLM context.
3. **Chunking with Metadata:** Chunks are grouped by section headings. Each chunk is tagged with rich metadata: `document_id`, `page_number`, `parent_heading`, and `document_classification_level`. This metadata is used for filtering and citation."

---

## Part 2: Secure RAG & Access Control

### 3. How do you implement Role-Based Access Control (RBAC) in a vector search database (like ChromaDB or PGVector) to prevent unauthorized data exposure?
**Expert Answer:**
"Vector similarity search has no built-in security layer; if you query the index, it returns matches regardless of who is asking. I implement document-level security at two levels:
1. **Metadata Filtering (Query Level):** During document ingestion, I attach an access control list (ACL) as metadata to each vector chunk (e.g., `{'allowed_roles': ['Finance-L2', 'Compliance']}`). When executing a similarity search, I pass a metadata filter matching the user's authenticated roles (extracted from their OAuth2 token):
   ```python
   results = vector_db.similarity_search(
       query="What is the department budget?",
       filter={"allowed_roles": {"$in": user_roles}}
   )
   ```
2. **Post-Filtering Verification:** In the FastAPI application layer, before merging the retrieved chunks into the LLM prompt, I re-verify the chunk ACLs against the active session token to prevent metadata spoofing. This ensures the LLM never receives context that the user is unauthorized to see."

---

## Part 3: EU AI Act & GDPR Compliance

### 4. What are the risk categories under the EU AI Act? How does this legislation affect the deployment of Generative AI applications (like RAG and LLMs) in Luxembourg?
**Expert Answer:**
"The EU AI Act classifies AI systems based on their risk level:
1. **Unacceptable Risk (Prohibited):** Systems like cognitive behavioral manipulation, social scoring, or real-time biometric identification in public spaces.
2. **High Risk:** Systems used in critical infrastructure, employment evaluation, credit scoring, or law enforcement. These require strict MLOps logs, high-quality data governance, detailed technical documentation, human oversight features, and conformity assessments before deployment.
3. **Limited/Minimal Risk (General LLMs/RAG):** Most generative chatbots fall here. They are subject to strict **transparency obligations**: users must be clearly notified that they are interacting with an AI system, and any generated content (like audio/video) must be watermarked.
4. **General Purpose AI (GPAI) Models:** LLM developers must provide technical documentation, respect copyright laws, and publish summaries of training data. High-impact GPAI models with systemic risks face additional evaluations and adversarial testing.
- **Deloitte Impact:** When deploying solutions for bank clients, we must perform a Risk Classification Assessment. If an AI system acts as a credit risk evaluator, it is classified as 'High Risk', requiring us to implement comprehensive audit logs, model performance monitoring, and human override buttons in the React UI."

### 5. How do you handle the GDPR 'Right to be Forgotten' (Article 17) in a system using vector databases and LLM caches?
**Expert Answer:**
"GDPR requires that when a user requests deletion, all their personal data must be erased. In an AI application, this involves three steps:
1. **Vector Index Deletion:** I ensure all embedded vectors are tagged with a unique `user_id` or `session_id` in their metadata. Upon receiving a deletion request, I issue a hard delete command to the vector database:
   ```python
   vector_db.delete(filter={"user_id": request.user_id})
   ```
2. **Conversation History Cleanups:** If we store chat memories or agent step chains in databases (like Postgres or Redis), I purge all rows matching the `user_id` or `session_id`.
3. **LLM Response Caching:** If we use semantic caching (like GPTCache) to save API costs, I must flush the cache keys associated with that user's historical queries. 
*Note:* We must ensure we do not use user interaction history to fine-tune our models unless explicit, separate consent was given. If a model was already fine-tuned on user data, purging that individual's data from model weights is extremely difficult (machine unlearning), which is why we strictly avoid using raw customer PII for model tuning."
