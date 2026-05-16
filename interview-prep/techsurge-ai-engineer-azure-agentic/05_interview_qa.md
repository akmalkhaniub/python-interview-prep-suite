# Interview Q&A: AI Engineer (Azure & Multi-Agent)

### Q1: Multi-Agent Orchestration
**Question:** How do you handle a "loop" in a multi-agent system where two agents keep passing a task back and forth without finishing?
**Answer:** This is a classic "Infinite Loop" failure. I handle this by implementing **Max Iterations** and **Stateful Flags**. In LangGraph, I add an `iteration_count` to the global state. If an agent tries to route back more than 5 times, a "Supervisor" node intervenes, forces a summary of the progress so far, and either requests user input or terminates the process with an error code.

### Q2: Multi-Modal RAG
**Question:** How would you implement a RAG system that needs to "read" a complex table in a scanned PDF and answer questions about specific rows?
**Answer:** I would use **Azure AI Document Intelligence** with the 'Layout' model to extract the table as a structured JSON/Markdown representation rather than just raw text. For the RAG pipeline, I would chunk the document such that tables are kept intact with their headers. During retrieval, the agent receives the structured table, and I use a "Reasoning Agent" to parse the specific rows requested, ensuring the answer is grounded in the table's structure.

### Q3: Azure Infrastructure
**Question:** Why use Azure API Management (APIM) for LLM deployments instead of just calling the Azure OpenAI endpoint directly?
**Answer:** APIM provides a necessary "abstraction layer" for production. It allows us to implement **Load Balancing** across multiple regional endpoints to handle high traffic, enforce **Rate Limiting** via policies (e.g., per-tenant quotas), and provide a unified **Audit Log** of all LLM traffic. It also enables us to swap out models or versions behind a single API endpoint without updating the application code.

### Q4: Production Guardrails
**Question:** What is a "Circuit Breaker" in the context of an AI Agent, and when should it trigger?
**Answer:** A Circuit Breaker protects the system from cascading failures. It should trigger if:
1.  **Upstream Failure:** The LLM API is returning 5xx errors or timing out consistently.
2.  **Safety Failure:** The Content Safety filter is repeatedly blocking the agent's output.
3.  **Cost Failure:** The agent has exceeded its per-task token budget.
Once "Open," the agent should stop execution immediately and notify the system to prevent wasted compute and poor UX.

### Q5: Hallucinations & Evaluation
**Question:** How do you objectively measure if your agent is hallucinating "citations" in a RAG system?
**Answer:** I use an automated **Citation Verification** node. After the agent generates a response with citations, I programmatically check if the cited text snippets actually exist in the retrieved documents. I also use a "Judge LLM" to check if the *meaning* of the citation supports the claim made by the agent. A high "Hallucination-on-Citation" score would trigger a prompt refinement or a change in the retrieval strategy.

### Q6: Multi-Modal Vision
**Question:** An agent needs to analyze a screenshot of a software error and open a ticketing system. How do you ensure the agent doesn't "hallucinate" the error details?
**Answer:** I implement a **Vision-to-Text extraction step** first. Instead of asking the agent "What is wrong in this image?", I ask a vision specialist node to "Describe every text element and error code found in this image." This structured description is then passed to a "Router Agent" which matches the extracted error code against a known knowledge base. This two-step process (Extraction -> Reasoning) significantly reduces hallucinations compared to one-step visual reasoning.

---

### Pro-Tips for the Interview:
*   **Mention Karachi:** If the interview is with the Karachi team, mention your understanding of local infrastructure challenges (like connectivity) and how your experience with **MedEdge's** offline-first mode addresses these.
*   **Emphasize "Auditability":** Techsurge is an enterprise company. They care about *how* the agent made a decision, not just that it did. Talk about "Tracing Spans."
*   **Software Engineering First:** Always frame your AI work within the context of robust software engineering (Python 3.12+, Pydantic, unit testing, CI/CD).
