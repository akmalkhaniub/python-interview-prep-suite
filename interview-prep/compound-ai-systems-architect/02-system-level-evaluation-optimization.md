# Module 02: System-Level Evaluation & Optimization

This module covers the strategies used to evaluate and benchmark complex compound AI systems, regression-test agents, and optimize latency and API costs.

---

## Technical Q&A

### Q1: What is a "Golden Dataset," and how do you design a robust CI/CD evaluation pipeline for a multi-agent system?
**Answer:**
A **Golden Dataset** is a curated, high-quality collection of input-output test cases that represent the diverse and challenging scenarios an AI system will face in production. Each test case typically contains:
- The input prompt/query.
- The reference context (if using RAG).
- The expected target/ground truth output.
- Custom metadata (e.g., category, complexity tier).

**Designing a CI/CD Evaluation Pipeline:**
1. **Triggering Evals:** Whenever a developer modifies prompt templates, agent logic, or model versions in the codebase, the GitHub Actions or GitLab CI/CD runner triggers the evaluation suite.
2. **Deterministic Running:** Run the golden dataset inputs through the agent pipeline using pinned seed parameters or deterministic settings (temperature = 0) where possible.
3. **LLM-as-a-Judge Evaluation:** Since regex matching fails for generative outputs, run a separate judge model (e.g., GPT-4o) using structured scoring templates to rate outputs against the ground truth on multiple dimensions (e.g., correctness, tone, safety).
4. **Assert Thresholds:** Assert that the aggregate score does not fall below a predetermined baseline (e.g., Average Correctness $\ge 0.85$, PII leak rate $= 0.00$). The pipeline fails if a regression is detected.
5. **Observability Tracking:** Upload execution traces and scoring runs to a monitoring platform (e.g., Langfuse, LangSmith) to audit failures.

---

### Q2: Explain the primary RAG metrics evaluated by frameworks like Ragas or DeepEval. Focus on Faithfulness, Answer Relevance, and Context Recall.
**Answer:**
RAG evaluation requires assessing the retriever and the generator independently. The three primary metrics are:

1. **Faithfulness (Groundedness):**
   - **What it measures:** Is the generated answer derived *solely* from the retrieved context, or does it contain hallucinations/outside knowledge?
   - **How it's computed:** The judge LLM extracts claims from the generated answer and checks if each claim is explicitly supported by the retrieved context chunks.
   - **Formula approximation:** $\frac{\text{Number of claims supported by context}}{\text{Total number of claims in generated answer}}$.

2. **Answer Relevance:**
   - **What it measures:** Does the generated answer directly address the user's initial question, or is it filled with redundant or off-topic information?
   - **How it's computed:** The judge LLM is shown the generated answer and asked to reconstruct the likely question that would yield this answer. The semantic similarity between the reconstructed question and the actual user question is measured.

3. **Context Recall:**
   - **What it measures:** Did the retriever fetch *all* the necessary information required to answer the question, based on the ground truth?
   - **How it's computed:** The claims in the ground truth answer are compared against the retrieved context chunks. If any ground truth claims cannot be found in the retrieved context, recall is low.
   - **Formula approximation:** $\frac{\text{Number of ground truth claims present in context}}{\text{Total number of claims in ground truth answer}}$.

---

### Q3: What strategies can an architect use to reduce latency and API costs in a production system running hundreds of agent steps per minute?
**Answer:**
To scale a compound system efficiently, implement the following architectural layers:

1. **Semantic Caching (e.g., GPTCache):**
   - Keep a vector database of previous user queries and successful agent responses.
   - Before running the full agent loop, embed the user query and run a similarity check. If a past query matches with high similarity (cosine distance $> 0.96$), serve the cached response immediately, bypassing LLM API costs and reducing latency to milliseconds.

2. **Routing & Tiering:**
   - Use a fast, low-cost classifier (or semantic router) to assess query complexity.
   - Route simple tasks (e.g., "Hello, system", "Summarize this email") to fast local or cheap models (e.g., Claude 3 Haiku, Llama-3-8B).
   - Reserve larger, expensive models (e.g., Claude 3.5 Sonnet) only for complex coding, multi-step planning, or multi-source reasoning.

3. **Streaming & Speculative Decoding:**
   - Enable token streaming to the client UI so the user perceives immediate response time (reducing Perceived Latency).
   - Use parallel execution: run retrieval, metadata checks, and database lookups concurrently using async gathering (`asyncio.gather`), rather than executing them sequentially.

4. **Structured Output Enforcement:**
   - Enforce structured outputs using model-native tool-calling (JSON mode) or JSON Schemas. This prevents models from wasting tokens on conversational fluff and prevents parsing errors that would require costly retry loops.
