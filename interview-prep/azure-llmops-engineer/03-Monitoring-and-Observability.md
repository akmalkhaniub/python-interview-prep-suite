# 03: Monitoring and Observability

Monitoring LLM systems goes beyond "Is the server up?" to "Is the answer still good?".

## 1. Core Metrics
*   **Latency**: 
    *   *Time to First Token (TTFT)*: Critical for user experience.
    *   *Total Response Time*: How long did retrieval + generation take?
*   **Cost & Usage**: 
    *   *Token Count*: Tracking prompt vs. completion tokens.
    *   *Request Count*: To manage Azure OpenAI TPM/RPM (Tokens/Requests Per Minute) limits.
*   **Operational**: Success/Failure rates of ingestion pipelines and API calls.

## 2. Advanced Observability
*   **Drift Detection**: 
    *   *Data Drift*: Has the type of incoming documents changed?
    *   *Embedding Drift*: Does the current vector store still accurately represent the data?
*   **Traceability**: Using **Application Insights** and **LangSmith** (or Azure native alternatives) to trace a specific request through retrieval chunks to the final prompt.

## 3. Azure Monitor & App Insights
*   **Custom Dimensions**: Logging metadata (e.g., model name, prompt version, user group) to App Insights for detailed dashboarding.
*   **Alerting**: Setting up alerts for 429 Errors (Rate Limiting) or when average latency exceeds a threshold.

## Interview Questions
1.  **"How would you build a dashboard to monitor Azure OpenAI costs?"**
    *   *Ans*: I would enable Diagnostic Settings on the Azure OpenAI resource to send logs to a Log Analytics Workspace. Then, I'd use Kusto (KQL) to query token usage and calculate costs based on the model's pricing.
2.  **"What is 'Embedding Drift' and how do you monitor it?"**
    *   *Ans*: It happens when the underlying data distribution changes so much that the existing vectors are no longer effective for retrieval. We monitor it by checking retrieval quality metrics over time or comparing the distribution of new embeddings to a baseline.
3.  **"How do you debug a slow RAG response using Azure tools?"**
    *   *Ans*: I use Application Insights' 'End-to-end transaction details'. I can see exactly how much time was spent in the vector search step vs. the LLM generation step.

## Practice Task
*   Write a KQL query to count the total tokens used per model in the last 24 hours.
