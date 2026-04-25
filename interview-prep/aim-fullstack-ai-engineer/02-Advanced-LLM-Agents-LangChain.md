# Advanced LLM Agents & LangChain
**Focus:** LangChain, Pinecone RAG, Streaming, Evaluations, Hallucination Prevention

---

## Section A: Advanced Retrieval & RAG

### Q1: Implementing Robust RAG with Pinecone
**Question:** How do you implement a robust Retrieval-Augmented Generation (RAG) pipeline using Pinecone and LangChain?

**Expected Answer:**
A robust RAG pipeline requires moving beyond simple naive document retrieval. First, the ingestion phase: I use LangChain document loaders to ingest care planning PDFs or protocols. The critical step is **Chunking**. I use semantic chunking or recursive character splitting with generous overlap to ensure context isn't severed mid-sentence. These chunks are embedded using an embedding model (like OpenAI's `text-embedding-3-large`) and upserted into a **Pinecone** vector database, alongside rich metadata (e.g., `document_type`, `care_home_id`, `date`).

During retrieval, I implement a **Query Transformation** step. Users rarely ask perfectly phrased questions. I use an LLM to rephrase or expand the user's query into a more optimized search vector. I then query Pinecone, explicitly utilizing **Metadata Filtering** (e.g., `filter: { care_home_id: user.home_id }`) before performing the vector similarity search. This guarantees absolute data isolation in a multi-tenant architecture. Finally, I pass the retrieved documents to the synthesis LLM, heavily relying on a strict system prompt instructing the model to *only* answer using the provided context.

---

### Q2: LLM Agents vs. LLM Chains
**Question:** What is the fundamental difference between an LLM Chain and an LLM Agent? When would you use each?

**Expected Answer:**
An **LLM Chain** is a deterministic, hardcoded sequence of operations. For example: Step 1: Extract keywords from input → Step 2: Query database with keywords → Step 3: Summarize results. The control flow is explicitly defined by the software engineer in TypeScript. Chains are highly predictable, fast, and ideal for well-defined, repetitive tasks like generating a shift summary from a specific JSON payload.

An **LLM Agent**, conversely, utilizes the LLM as a reasoning engine to determine the control flow dynamically. The engineer provides the Agent with an objective and a toolkit (via MCP or LangChain tools). The Agent uses a framework like ReAct (Reason + Act) to observe the goal, decide which tool to use, execute it, observe the output, and determine if it needs to use another tool or if it has solved the problem. Agents are non-deterministic and slower, but they are essential for complex, open-ended tasks like "Analyze the rota discrepancies for next week and draft an email to the affected staff," where the exact sequence of required steps cannot be known in advance.

---

## Section B: Production LLM Engineering

### Q3: Streaming LLM Responses
**Question:** How do you implement streaming responses for LLMs, and why is this critical for the user experience?

**Expected Answer:**
Because LLMs generate text auto-regressively (token by token), generating a complex care report might take 10-15 seconds. If the frontend waits for the entire generation to complete before rendering, the user experiences a frozen UI and will likely abandon the application.

To solve this, I utilize **Server-Sent Events (SSE)** or WebSockets to stream tokens to the frontend as they are generated. In a Node.js/Next.js stack, I use the AI SDK (like Vercel AI SDK) or LangChain's `.stream()` methods. The backend establishes a persistent connection with Anthropic/OpenAI, requesting the response in `stream: true` mode. As chunks of tokens arrive at the backend, they are instantly piped through the Express/Next.js API route directly to the React frontend, which updates the UI state progressively. This drops the perceived latency (Time To First Token) to milliseconds, keeping the user engaged while the full response resolves.

---

### Q4: Evaluating LLM Performance
**Question:** How do you manage and evaluate LLM agent performance and accuracy in a production environment?

**Expected Answer:**
Evaluating non-deterministic LLMs requires systematic "LLMOps." I cannot rely on standard unit tests (checking if `output == expected_string`). Instead, I build an evaluation suite using frameworks like LangSmith or promptfoo.

I maintain a "Golden Dataset" of hundreds of varied historical user prompts and expected ground-truth answers or expected tool executions. In CI/CD, before a new system prompt or model version is deployed, the pipeline runs the Golden Dataset through the agent. Because the output text will vary, I use an "LLM-as-a-Judge" approach: a powerful model (like GPT-4o) evaluates the agent's output against the ground truth, scoring it on metrics like Helpfulness, Relevance, and Faithfulness (no hallucinations). In production, I trace every execution, capturing the prompt, retrieved context, and output, allowing me to continuously identify edge cases, update the Golden Dataset, and refine the prompts.

---

### Q5: Preventing Hallucinations in Healthcare
**Question:** How do you prevent LLM hallucination when generating critical outputs, such as medical or compliance reports?

**Expected Answer:**
In a healthcare context, an LLM confidently inventing a patient medication or compliance protocol is a catastrophic failure. Preventing hallucination requires a multi-layered architectural defense. 

First is **Strict Grounding via RAG**. The system prompt must explicitly state: "You are a care assistant. Answer ONLY using the provided `<context>`. If the answer is not contained in the context, output exactly: 'I do not have sufficient information to answer this.'" 
Second, I utilize **Temperature Control**, setting the model's temperature to `0.0` or `0.1` to eliminate creative variance and force highly deterministic outputs.
Third, I implement **Citation Enforcing**. The prompt requires the LLM to append a specific document ID or paragraph reference to every claim it makes. If the backend parser detects a claim without a valid citation pointing to the retrieved context, the output is flagged or rejected. Finally, for critical reports, the LLM is relegated to a "drafting" role; a human supervisor must review and sign off on the generated report before it is committed to the system of record.
