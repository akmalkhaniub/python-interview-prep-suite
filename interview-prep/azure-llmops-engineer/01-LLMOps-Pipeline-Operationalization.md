# 01: LLMOps Pipeline Operationalization

Operationalizing RAG means moving from a notebook to a robust, scalable, and monitored system.

## 1. Productionizing RAG Pipelines
*   **Ingestion Step**: Automating the pull from SharePoint via Graph API. Handling rate limits and retry logic.
*   **Processing Step**: Using Azure Functions to trigger on new file uploads. Functions perform classification and initial layout parsing.
*   **Indexing Step**: Pushing cleaned and enriched data to Azure AI Search. Managing index versions and schema updates.

## 2. Orchestration Tools
*   **Azure Data Factory (ADF)**: Best for high-volume, batch ingestion and movement. Can trigger Azure ML pipelines or Azure Functions.
*   **Azure Functions**: Best for event-driven, real-time processing (e.g., "new file in blob -> process").
*   **LangChain**: Used within the code to manage the retrieval and generation logic, but must be "wrapped" in a production-ready API (FastAPI).

## 3. Handling Unstructured Data
*   **Document Intelligence**: Integrating OCR and layout parsing into the pipeline.
*   **Enrichment**: Adding AI-generated metadata (e.g., summary, detected intent) at ingestion time to improve retrieval precision.

## Interview Questions
1.  **"How do you operationalize a LangChain pipeline in Azure?"**
    *   *Ans*: I wrap the LangChain logic in a FastAPI service, containerize it with Docker, and deploy it to Azure App Service or AKS. I use Azure Key Vault for API keys and Application Insights for tracing.
2.  **"Explain the role of Azure Data Factory in an LLM pipeline."**
    *   *Ans*: ADF acts as the heavy-duty orchestrator. It manages the movement of raw data from sources like SharePoint to the lake, triggers the enrichment functions, and ensures that the vector store index stays updated.
3.  **"How would you handle a failure in the embedding generation step of a pipeline?"**
    *   *Ans*: I would implement a retry-with-backoff policy in the Azure Function or ADF activity. If it fails repeatedly, I push the message to a Dead Letter Queue (DLQ) for manual review.

## Practice Task
*   Draw a flow diagram showing a file's journey from SharePoint through Azure Functions (processing) to Azure AI Search (indexing).
