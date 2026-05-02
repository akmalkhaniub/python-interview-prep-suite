# 04 - JS/TS & Node.js for AI SDKs

## Node.js & AI

### 1. How do you stream LLM tokens to a frontend using Node.js?
**Answer:**
Using **Server-Sent Events (SSE)** or **Readable Streams**.
- **Process:** The Node.js server receives a stream from the local engine (Llama.cpp) and pipes it to the `res` object with `Content-Type: text/event-stream`.

### 2. Why use TypeScript for an AI SDK?
**Answer:**
- **Type Safety:** Preventing runtime errors when handling complex JSON payloads from LLMs.
- **Autocompletion:** Developers get immediate feedback on API parameters (e.g., `temperature`, `max_tokens`).
- **Documentation:** The types themselves serve as living documentation.

### 3. How to handle "Asynchronous Operations" in an AI application?
**Answer:**
- **Async/Await:** For sequential logic.
- **Promise.all:** For parallel tasks (e.g., embedding multiple chunks).
- **Web Workers / Worker Threads:** To prevent the main thread from blocking during heavy data processing (though local inference usually happens in C++).

## SDK Development

### 4. What is a "Wrapper" in the context of Llama.cpp?
**Answer:**
A Node.js library (like `node-llama-cpp`) that uses N-API or child processes to call the underlying C++ binaries. It provides a familiar JS interface for web developers.

### 5. How do you implement "Context Memory" in a Node.js AI app?
**Answer:**
By maintaining a `messages` array in the application state and passing the last $N$ messages (within the token limit) to the model for each new query.

### 6. Best practices for "Local Model Management"?
**Answer:**
- **Automatic Downloads:** Using a tool like `huggingface-hub` to download GGUF models.
- **Path Management:** Storing models in a standard local directory (e.g., `~/.cache/qvac/models`).
- **Verification:** Checking hashes to ensure the model file isn't corrupted.
