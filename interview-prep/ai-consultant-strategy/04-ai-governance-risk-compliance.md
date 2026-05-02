# 04 - AI Governance, Risk & Ethics

## Risk & Compliance

### 1. What are the key risks of deploying LLMs in Enterprise?
**Answer:**
- **Hallucination:** Providing false information as fact.
- **Data Leakage:** PII or trade secrets leaking into the model's training data or public APIs.
- **Prompt Injection:** Malicious users tricking the model into bypassing safety filters.
- **Bias:** Replicating societal biases present in the training data.

### 2. How do you comply with GDPR in an AI project?
**Answer:**
- **Right to be Forgotten:** Ensure users can have their data removed from RAG databases.
- **Explainability:** For models making life-impacting decisions (e.g., credit), you must be able to explain *why* a decision was made.
- **Data Minimization:** Only use the data necessary for the specific AI task.

### 3. What is an "AI Ethics Board"?
**Answer:**
A cross-functional group (Legal, Tech, HR, Ethics) that reviews AI projects against the company's values and safety guidelines before deployment.

## Governance Frameworks

### 4. How do you detect and mitigate Bias in models?
**Answer:**
- **Diverse Datasets:** Ensure training data represents all demographics.
- **Disparate Impact Analysis:** Check if the model performs significantly differently for different groups.
- **Bias Audits:** Use tools like Fairlearn or Aequitas.

### 5. What is "Explainable AI" (XAI)?
**Answer:**
Techniques like **SHAP** or **LIME** that identify which features were most important for a specific prediction. For LLMs, it involves techniques like "Self-Correction" or "Source Attribution" (citing sources in RAG).

### 6. Importance of "AI Usage Policies"?
**Answer:**
A document defining what employees *can* and *cannot* do with AI (e.g., "Do not paste customer names into public ChatGPT"). It establishes legal and operational boundaries for the organization.
