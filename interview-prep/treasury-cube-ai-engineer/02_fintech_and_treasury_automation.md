# Deep Dive: FinTech, Treasury, & Agentic Automation

Treasury is about **accuracy, auditability, and speed**.

## 1. ISO 20022 & Data Extraction

The "daily grind" at Treasury Cube involves processing bank statements.
*   **CAMT.053:** The global standard for bank-to-customer statements.
*   **Challenge:** Even with ISO 20022, different banks use "enrichment" fields differently.
*   **Solution:** Use **Azure AI Document Intelligence** with a **Neural Model** to map various bank formats to a unified internal "Treasury Cube" schema.

## 2. Agentic Workflows with Durable Functions

Automation in Treasury often requires human-in-the-loop (HITL) for high-value payments.
*   **Scenario:** A payment over $500k is flagged as "anomalous" by an AI model.
*   **Workflow:**
    1.  **Azure Function:** Triggers AI anomaly detection.
    2.  **Durable Entity:** Maintains the state of the "Investigation."
    3.  **Human Interaction:** Wait for a treasurer to approve/reject via a Slack/Teams notification (via Logic Apps).
    4.  **Final Action:** Execute the payment through a SWIFT gateway.

## 3. Conversational Treasury: "The CFO Assistant"

Building trust with a CFO requires more than just "chat."
*   **Citations:** Every number the assistant gives must be linked to a specific transaction ID or bank statement line.
*   **Deterministic Logic:** If a user asks "What is my balance?", the AI shouldn't "calculate" it. It should call a `get_balance()` tool that runs a SQL query. The AI only *summarizes* the result.
*   **Confidence Scores:** If the retrieval context is low-quality, the assistant should say "I am not sure, please check the dashboard manually" instead of guessing.

## 4. Monitoring & Cost Control

*   **Cost-per-Query:** In a multi-tenant SaaS, you need to know which customers are "expensive" in terms of GPT-4 token usage.
*   **Content Safety:** Using Azure Content Safety to detect if a treasurer is trying to use the chatbox to exfiltrate bulk PII or sensitive data.

## Interview Questions
1.  "How do you design a 'Human-in-the-loop' system for AI-generated payment instructions?"
2.  "What is ISO 20022, and why is it important for a Treasury AI Engineer to understand it?"
3.  "How would you approach building a 'What-If' simulator for FX exposure using an LLM?"
