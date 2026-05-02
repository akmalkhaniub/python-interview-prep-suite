# 04 - Evaluation & Guardrails

## Evaluation Metrics

### 1. How do you evaluate an LLM's output for quality?
**Answer:**
- **Deterministic:** Exact match, JSON validation (using Pydantic).
- **Heuristic:** Semantic similarity (using embeddings), BLEU/ROUGE (for summarization).
- **LLM-as-a-judge:** Using a more powerful model (e.g., GPT-4o) to grade the response of a smaller model based on a rubric (accuracy, tone, helpfulness).
- **Human Eval:** The gold standard. A/B testing two model versions with users.

### 2. What is the "Evals" library by OpenAI?
**Answer:**
A framework for evaluating LLMs on standardized datasets. It allows you to run "evals" (tests) and see how your prompt or fine-tuning changes affect performance across hundreds of test cases.

### 3. How do you measure RAG quality? (The RAG Triad)
**Answer:**
- **Context Relevance:** Is the retrieved context actually useful for the query?
- **Groundedness (Faithfulness):** Is the answer derived ONLY from the retrieved context?
- **Answer Relevance:** Does the final answer actually address the user's query?

## Guardrails & Safety

### 4. What are "Guardrails" for LLMs?
**Answer:**
Software layers (like **NVIDIA NeMo Guardrails** or **Guardrails AI**) that check the input and output of the LLM.
- **Input Rails:** Prevent prompt injection or off-topic queries.
- **Output Rails:** Ensure the output is in the correct format (e.g., valid JSON) and doesn't contain PII or toxic content.

### 5. How do you prevent "Prompt Injection"?
**Answer:**
- **System Message Isolation:** Use clear delimiters for user input.
- **Filtering:** Detect keywords like "Ignore all previous instructions."
- **Least Privilege:** Don't give the LLM API keys or tools it doesn't need.
- **Monitoring:** Log and audit anomalous queries.

### 6. Explain the concept of "Constitutional AI."
**Answer:**
A method developed by Anthropic where the model is given a set of principles (a "constitution") and uses them to self-critique and revise its own responses to ensure they are helpful, harmless, and honest.
