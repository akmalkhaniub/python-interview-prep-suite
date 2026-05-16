# Agentic Patterns & Production Controls (Azure/LangGraph)

This document focuses on the **architectural patterns** mentioned in the Techsurge JD, specifically for production-grade agentic systems.

---

## 1. The Planner-Router-Specialist-Validator Pattern
The JD explicitly asks for this. Here is how to explain it in **LangGraph**:

1.  **Planner:** Takes a complex goal (e.g., "Onboard this user and check their credit history"). It breaks it into sub-tasks and updates the `GraphState`.
2.  **Router:** An LLM node that looks at the current state and decides which specialist to call next.
3.  **Specialists:** Dedicated nodes for specific tasks (e.g., `CreditCheckTool`, `EmailSender`). They operate with "Safe Boundaries" (restricted API scopes).
4.  **Validator:** A "Critic" node that checks the Specialist's output against the Planner's sub-task. If it fails, it routes back to the Specialist with a "Reflection" prompt.

**Talking Point:** "I design my agents as **state machines** in LangGraph. This ensures that the execution path is deterministic and auditable, which is critical for the enterprise systems Techsurge builds."

---

## 2. Production Controls (The "Five Pillars")
The JD lists: retries, timeouts, circuit breakers, rate limits, and token/cost budgets.

| Control | Implementation Strategy |
| :--- | :--- |
| **Retries** | Use exponential backoff for transient API errors (429, 503). LangGraph supports this at the node level. |
| **Timeouts** | Implement `AbortController` or similar logic to kill an LLM call if it takes > 30s, preventing UI hang. |
| **Circuit Breakers** | If the LLM returns 3 consecutive errors, the "Circuit Opens." The system stops calling the LLM and returns a graceful fallback message or alerts an engineer. |
| **Rate Limits** | Enforced via Azure API Management (APIM) to prevent a single user from draining the token pool. |
| **Token/Cost Budgets** | Calculate `total_tokens` usage in the `GraphState` after each node. If `state.cost > threshold`, trigger a "Safe Stop Condition." |

---

## 3. Multi-Modal RAG Grounding
**Requirement:** Grounding and structured/cited responses.

*   **Pattern:** **Citation Extraction.**
    *   During the retrieval step, keep track of the source `document_id`, `page_number`, and `bounding_box` (for images).
    *   Prompt the LLM to provide citations in a specific JSON format: `{"text": "...", "source": "doc_A.pdf", "page": 4}`.
    *   **Validation:** A separate node verifies that the cited text actually exists in the retrieved context to prevent "Citation Hallucination."

---

## 4. AgentOps & Hallucination Analysis
**Requirement:** Hallucination/grounding accuracy and structured error analysis.

*   **Approach:** **LLM-as-a-Judge.**
    *   Create a "Validator" dataset of (Question, Context, Ground Truth Answer).
    *   Run the agent pipeline.
    *   Use a separate, high-reasoning model (e.g., GPT-4o or Gemini 1.5 Pro) to grade the response:
        *   **Faithfulness:** Is the answer derived *only* from the context?
        *   **Relevance:** Does the answer address the question?
        *   **Hallucination Rate:** Percentage of responses with non-grounded claims.
