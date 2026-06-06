# Module 04: Agentic Workflows, Evaluation & Observability

In debt collections and lending, agents make automated decisions (e.g., offering loan restructure terms, rescheduling collection dates). As an Engineering Manager, you must build robust agentic state machines, set up evaluation guardrails, and monitor production runs to prevent financial or brand liability.

---

## 1. Designing Agentic State Machines
Instead of unstructured agent loops that can run endlessly or take unpredictable paths, we design **deterministic agentic state machines** (using tools like LangGraph or state charts):

```
                        +----------------------------+
                        |         Start Call         |
                        +--------------+-------------+
                                       |
                                       v
                        +----------------------------+
                        |      Welcome / Intro       |
                        +--------------+-------------+
                                       |
                                       v
                        +----------------------------+
                        |    Verify Borrower Identity| <---+ (Retry Loop, max 3)
                        +--------------+-------------+     |
                                       |                   |
                        +--------------+-------------+     |
                        |      Identity Verified?    |-----+
                        +--------+-------------+-----+
                                 | Yes         | No
                                 v             v
      +----------------------------+         +----------------------------+
      |    Negotiate Repayment     |         |   Transfer to Human / End  |
      +--------------+-------------+         +----------------------------+
                     |
                     v
      +----------------------------+
      |  Confirm Payment Details   |
      +--------------+-------------+
                     |
                     v
      +----------------------------+
      |      Log commitment        |
      +----------------------------+
```

*   **System Prompt Isolation:** Each state has its own specialized prompt and set of tools (e.g., a "Repayment Negotiation" state only has tools like `get_payment_plans` or `calculate_discount`, reducing model hallucination risk).
*   **Structured Outputs:** Enforce outputs using Pydantic schemas at each state transition.

---

## 2. Evaluation Suites (Ragas & TruLens)
We cannot launch prompt updates directly to production without verification. We build automated offline and online evaluation pipelines:

### The RAG Triad (TruLens / Ragas)
1.  **Context Relevance:** Is the retrieved document text relevant to the query? (Protects against fetching irrelevant customer files).
2.  **Groundedness (Hallucination Detection):** Is the model response based *only* on the retrieved context? (Ensures the agent doesn't offer fake payment terms).
3.  **Answer Relevance:** Does the response address the user's input directly?

### Prompt Safety & Alignment
*   **Toxicity & Brand Guidelines:** Using auxiliary LLMs to grade output history for politeness and tone.
*   **Compliance Verification:** Validating that terms required by the RBI (Reserve Bank of India) are mentioned (e.g., the name of the lender, clear penalty fee percentages).

---

## 3. Production Observability & Monitoring
We implement OpenTelemetry-based tracing (via tools like LangSmith, Langfuse, or Arize Phoenix):
*   **Span Tracing:** Trace the entire lifecycle of a call turn: `WebSocket Ingest -> ASR -> LLM API -> Prompt Render -> DB Query -> TTS -> Playback`.
*   **Token tracking:** Log input/output tokens per session to track costs.
*   **Latency Monitoring:** Track P50, P90, and P99 latency spikes on LLM API providers.

---

## 4. Target Interview Questions & High-Score Answers

### Q1: How do you design an agentic pipeline that integrates with core banking systems while guaranteeing the agent cannot perform unauthorized transactions or leak sensitive user data?
**Answer:**
We guarantee security and determinism by implementing the following architecture:
1.  **State Isolation:** The agent logic does not directly connect to core banking systems. It emits structured transition events (e.g., `INITIATE_PRE_PAYMENT_DISCOUNT`) to an orchestrator backend.
2.  **API Gateway Guardrails:** The orchestrator backend validates the transaction request (e.g., checking if the customer is eligible for a discount and verifying their KYC token) before calling the banking API.
3.  **Role-Based Access Control (RBAC):** LLM tool calls must be authenticated via session tokens. The LLM only receives redacted data (e.g., masking credit card or account numbers, using database IDs instead).
4.  **Deterministic Fallback:** If the LLM produces a tool call command that fails validation or is outside the customer's state scope, the orchestrator overrides the LLM, logs a warning, and transitions the call to a human operator.

### Q2: What is your strategy for evaluating new LLM prompts or RAG documents offline before deploying them to production?
**Answer:**
We run a automated CI/CD evaluation harness:
1.  **Golden Dataset:** We maintain a curated dataset of ~500 real collection scenarios, containing typical borrower excuses, questions, and edge cases with expected ground-truth answers.
2.  **Batch Simulation:** When a developer pushes a prompt update, a GitHub Action runs a parallel simulation using the new prompt against the Golden Dataset (using GPT-4o as a grader).
3.  **Ragas Grading:** We calculate metrics: groundedness, context recall, and tone compliance.
4.  **Deployment Gate:** The prompt update is blocked if the groundedness score falls below 0.95 or if any brand compliance check fails. If it passes, it is deployed as a canary release to 5% of calls before a full rollout.

### Q3: How do you handle cost control and rate limiting when running agentic loops? What happens if an agent gets stuck in an infinite tool-calling loop?
**Answer:**
We implement runtime limits at the orchestrator layer:
1.  **Step Limits:** We enforce a maximum limit of 3 agent turns (LLM calls) per single conversation response. If the limit is reached, we force-transition the agent to a fallback state ("I am processing your request, one moment please") or route to a human.
2.  **Rate Limiting:** We implement Token Bucket rate-limiting on both external LLM APIs and our internal services.
3.  **Cache Layer:** We cache identical prompts and queries (e.g., static policy checks) using Redis to avoid invoking the LLM.
4.  **Strict Parsers:** We use strict JSON parsers (Pydantic/Instructor). If the output structure fails to parse 2 times in a row, the turn is cancelled.

### Q4: How do you implement LLM Guardrails in real-time? How do you ensure your collections voice agent doesn't threaten the customer or break Indian recovery guidelines?
**Answer:**
In India, the RBI has strict guidelines regarding debt recovery (e.g., no harassment, calls only between 8 AM and 7 PM, polite conduct). We enforce compliance via three defensive layers:
1.  **System Prompt Constraints:** We write explicit "Negative Constraints" (e.g., "NEVER use aggressive words," "DO NOT mention legal action unless explicitly authorized by the bank state").
2.  **Real-Time Guardrail Model (Llama Guard / Guardrails AI):** We run a fast classification model (e.g., a fine-tuned DistilBERT or Llama Guard) on the LLM output text *before* sending it to the TTS engine. If it detects non-compliant terminology, it blocks the response and substitutes a pre-approved compliance statement.
3.  **Post-Call Audit:** All transcriptions are audited by an offline pipeline that flags compliance anomalies for supervisor review.
