# 03: LLM Frameworks and APIs

Proficiency in LangChain/LlamaIndex and FastAPI is a core requirement.

## 1. LangChain vs. LlamaIndex
*   **LangChain**: Best for complex "chains" of thought, agents, and custom tool use. Very modular and generic.
*   **LlamaIndex**: Best for "Data-Centric" AI. Excellent for indexing, connecting to diverse data sources, and RAG-specific retrieval patterns.
*   **Recommendation**: In many enterprise RAG apps, LlamaIndex is used for ingestion/retrieval, while LangChain or custom code is used for the orchestration.

## 2. FastAPI for AI Services
*   **Async/Await**: Critical for handling long-running LLM calls without blocking the event loop.
*   **Streaming**: Using `StreamingResponse` to provide ChatGPT-like typing effects in the UI.
*   **Pydantic**: For strict validation of input prompts and output JSON schemas.
*   **Middleware**: Handling CORS, Auth, and Logging for AI workloads.

## 3. Agents and Tool Use (Function Calling)
*   **Function Calling**: Passing tool definitions to the LLM so it can decide when to call an external API (e.g., search the web, query a DB).
*   **ReAct Pattern**: Reason + Act. The loop where the LLM thinks, takes an action, observes the result, and repeats.

## Interview Questions
1.  **"How do you handle streaming responses in FastAPI for an LLM chatbot?"**
    *   *Ans*: Use `StreamingResponse` and a generator function that yields tokens as they come from the OpenAI `stream=True` call.
2.  **"What is the advantage of using LlamaIndex's Document object over a raw string?"**
    *   *Ans*: It preserves metadata (ID, tags, relationships) and handles chunking/parsing logic internally.
3.  **"How do you implement rate limiting in a FastAPI service talking to Azure OpenAI?"**
    *   *Ans*: Use middleware or a library like `fastapi-limiter`. Also, handle 429 errors from Azure with exponential backoff.

## Practice Task
*   Write a simple FastAPI endpoint that takes a user query, calls an Azure OpenAI model using LangChain, and returns a JSON response with the answer and tokens used.
