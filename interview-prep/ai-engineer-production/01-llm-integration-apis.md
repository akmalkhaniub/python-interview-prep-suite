# 01 - LLM Integration & APIs

## API Mechanics

### 1. What is the difference between Stateless and Stateful LLM APIs?
**Answer:**
- **Stateless:** The API (like OpenAI's `/v1/chat/completions`) doesn't remember previous messages. You must send the entire conversation history in every request.
- **Stateful:** The API (like OpenAI's **Assistants API**) maintains the thread/conversation state on the server. You only send the new message and the `thread_id`.

### 2. How does "Streaming" work in LLM APIs?
**Answer:**
It uses **Server-Sent Events (SSE)** to deliver tokens as they are generated, rather than waiting for the entire completion. This significantly improves the **Time to First Token (TTFT)** and perceived user experience.

### 3. What is "Function Calling" (Tool Use)?
**Answer:**
A feature where you provide the model with a JSON schema of your functions. The model decides which function to call and with what arguments. It doesn't *execute* the code; it returns a JSON object that your application then uses to call the local function.

## Reliability & Latency

### 4. How do you handle "Rate Limiting" and "Timeouts" from LLM providers?
**Answer:**
- **Exponential Backoff:** Retrying failed requests with increasing delays.
- **Circuit Breakers:** Stopping requests to a failing provider and switching to a fallback (e.g., switching from GPT-4 to Claude 3.5 Sonnet).
- **Fallbacks:** Using a smaller, cheaper model if the main one is slow/expensive.

### 5. What are "Logprobs" and how can they be used?
**Answer:**
The log-probability of each token being selected. They can be used for:
- **Confidence Scoring:** High logprob = High confidence.
- **Classification:** Selecting the label with the highest probability.
- **Beam Search:** Custom decoding strategies.

### 6. Explain the concept of "Context Window" management.
**Answer:**
LLMs have a finite limit (e.g., 128k tokens). Management involves:
- **Trimming:** Removing the oldest messages.
- **Summarization:** Replacing a chunk of history with a summary.
- **Selection:** Only including the most relevant documents (RAG).
