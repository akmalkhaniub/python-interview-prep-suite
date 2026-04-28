# 04: Security, Governance, and Compliance

In an enterprise setting, security is non-negotiable. MLOps ensures the AI doesn't become a vulnerability.

## 1. Network & Infrastructure Security
*   **VNet & Private Endpoints**: Ensuring that traffic between your application, Azure OpenAI, and Azure AI Search never crosses the public internet.
*   **Managed Identities**: Using system-assigned or user-assigned identities instead of API keys in code.
*   **Azure Key Vault**: Storing secrets, certificates, and keys securely.

## 2. AI Governance & Safety
*   **Guardrails**: Using **Azure AI Content Safety** to filter out harmful content (Self-harm, Hate, Violence).
*   **PII Redaction**: Implementing logic (e.g., using Presidio or Azure native tools) to detect and mask Personally Identifiable Information before it reaches the LLM or the vector store.
*   **RBAC (Role-Based Access Control)**: Restricting who can deploy models, update prompts, or access specific document indexes.

## 3. Compliance
*   **Data Residency**: Ensuring models and data are pinned to specific Azure regions (e.g., `East US` or `North Europe`) to comply with legal requirements.
*   **Audit Logging**: Capturing every request and response (if allowed) for audit traceability.

## Interview Questions
1.  **"How do you implement PII redaction in a RAG pipeline?"**
    *   *Ans*: I insert a step in the ingestion pipeline (and potentially the query pipeline) that uses a library like Microsoft Presidio or Azure's Text Analytics PII detection. Any detected entities are replaced with placeholders (e.g., `<PERSON_NAME>`).
2.  **"Why use Private Endpoints for Azure OpenAI?"**
    *   *Ans*: To prevent exposure to the public internet and protect against data exfiltration. It ensures that only authorized resources within the VNet can communicate with the AI service.
3.  **"How do you manage prompt versions while maintaining audit traceability?"**
    *   *Ans*: By storing prompts in Git. Every deployment has a unique ID linked to a specific Git commit hash. This provides a clear audit trail of what was deployed, by whom, and when.

## Practice Task
*   List the Azure resources needed to set up a secure, VNet-isolated RAG system.
