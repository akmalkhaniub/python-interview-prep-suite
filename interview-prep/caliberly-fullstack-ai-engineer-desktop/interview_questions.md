# Interview Questions & Expert Answers: Full-Stack AI Engineer (TypeScript/Tauri/MCP)

This document contains specialized technical questions and high-level, expert-grade answers designed for the Full-Stack AI Engineer role. It focuses on the core technologies listed in the JD: React 19, NestJS 11, Bun, Tauri v2, ChromaDB 3, LangChain, Model Context Protocol (MCP), Socket.IO, and advanced testing methodologies.

---

## Part 1: Core Stack (React 19, NestJS 11, Bun, Socket.IO)

### 1. What are the key advantages of using Bun as the runtime toolchain compared to standard Node.js for an AI-native full-stack application?
**Expert Answer:**
"Bun provides three primary advantages in this context:
1. **Speed & Startup Time:** Bun is written in Zig and powered by WebKit's JavaScriptCore (JSC) engine, which starts up significantly faster than Node.js (V8). This is critical for local desktop apps (packaged with Tauri) where cold-start latency impacts user experience.
2. **Built-in Tooling:** Bun replaces npm/pnpm, Node, ts-node, and Jest out-of-the-box. It executes TypeScript files directly without a separate compilation step (using its built-in transpiler) and has a built-in bundler/test runner.
3. **Native API Speed:** Bun has highly optimized built-in APIs. For example, `Bun.serve()` is up to 4x faster than Node's HTTP module, and its file I/O operations (`Bun.file()`) are dramatically faster. This speed is vital when streaming large data files or local document embeddings for RAG pipelines."

### 2. React 19 introduces several new features like the React Compiler and native support for async Actions. How do these help when building highly interactive AI interfaces?
**Expert Answer:**
"React 19 streamlines state management and rendering performance for AI applications:
- **React Compiler (React Forget):** AI UIs often have complex layouts displaying real-time streaming tokens, chat history, and contextual code panels. Previously, we had to carefully use `useMemo` and `useCallback` to prevent unnecessary re-renders during token streaming. The React Compiler automatically optimizes rendering at compile time, eliminating this boilerplate.
- **Async Actions:** AI requests (like generating text, embeddings, or executing local tool calls) are inherently asynchronous. React 19's Actions allow us to pass async functions directly to HTML elements (e.g., `<form action={handleSubmit}>`). React automatically manages the pending state, error handling, and optimistic updates, removing the need for manual `isPending` state hookups.
- **The `use` Hook:** It allows reading Promises directly in render. We can trigger an LLM search or document fetching promise and pass it to a component, which will suspend natively using `<Suspense>` until the LLM completes or streams."

### 3. How would you architect a real-time token streaming pipeline from a NestJS 11 backend to a React frontend using Socket.IO?
**Expert Answer:**
"To stream tokens in real-time under NestJS 11, I utilize an event-driven architecture with RxJS Observables:
1. **NestJS Gateway:** I define a Socket.IO Gateway using `@WebSocketGateway()`.
2. **LLM Event Stream:** In the NestJS service, when calling a chat model (via LangChain), I invoke the model with a streaming handler (like `model.stream(messages)`). This returns an async generator.
3. **RxJS Observable mapping:** I wrap the generator in an RxJS Observable. Each emitted token is mapped to a WebSocket event packet.
4. **Gateway Emission:** The gateway subscribes to this observable and emits a custom `token` event to the client room:
   ```typescript
   @SubscribeMessage('generateText')
   handleGeneration(@MessageBody() data: PromptDto, @ConnectedSocket() client: Socket) {
     return this.aiService.streamResponse(data.prompt).pipe(
       map(token => ({ event: 'token', data: token }))
     );
   }
   ```
5. **Frontend Consumption:** On the React side, a custom hook listens to the Socket.IO client, appends incoming tokens to the local Zustand store, and renders them instantly, ensuring fluid token rendering."

---

## Part 2: AI Orchestration & Vector Search (ChromaDB, LangChain, RAG)

### 4. ChromaDB 3 uses Hierarchical Navigable Small World (HNSW) indexing. Can you explain how HNSW works and how vector distance metrics (Cosine, L2, Inner Product) influence semantic search?
**Expert Answer:**
"HNSW is a graph-based algorithm for Approximate Nearest Neighbor (ANN) search in high-dimensional vector spaces:
- **How HNSW works:** It builds a multi-layer graph structure where the top layers have sparse connections (for fast, long-distance skips) and the bottom layers have dense connections (for precise local searches). Search starts at the top layer, greedily traverses to the closest node, drops down a layer, and repeats. This reduces search time from linear $O(N)$ to logarithmic $O(\log N)$, which is essential when querying thousands of local code chunks in a desktop app.
- **Distance Metrics:**
  - **Cosine Similarity:** Measures the angle between vectors, ignoring magnitude. It is the default for text embedding search because it normalizes for text length.
  - **L2 Distance (Euclidean):** Measures the straight-line distance between two points. It is highly sensitive to vector magnitude.
  - **Inner Product (IP):** Measures the dot product. If vectors are normalized to unit length (magnitude of 1), Inner Product is mathematically equivalent to Cosine similarity but runs much faster because it avoids division operations."

### 5. How do you design a stateful agentic system using LangChain that can dynamically decide whether to search local files or call an external LLM?
**Expert Answer:**
"I use LangGraph (part of the LangChain ecosystem) to construct a stateful, cyclic graph:
1. **Define State:** I define a shared state interface containing the message history and a list of retrieved documents.
2. **Define Nodes:**
   - **Agent Node:** Calls the LLM (e.g., GPT-4o or Claude 3.5) with the conversation history. The LLM is bound to tools (JSON schemas defining what functions can be called).
   - **Local File Search Tool Node:** A function that takes a query, searches local file indices in ChromaDB, and returns search results.
3. **Define Conditional Edges:** I check the output of the Agent Node. 
   - If the LLM requests a tool call (e.g., `search_local_files`), the routing edge redirects to the Search Tool Node. The search result is appended to the message history as a tool response, and the graph loops back to the Agent Node.
   - If the LLM decides it has enough context to answer, the edge routes to the final output node.
This structure allows the agent to iteratively retrieve local context, analyze it, decide if it needs more files, and then formulate a response."

---

## Part 3: Model Context Protocol (MCP)

### 6. What is the Model Context Protocol (MCP) and how does it improve tool-calling architectures for local AI applications?
**Expert Answer:**
"Model Context Protocol (MCP) is an open standard developed by Anthropic that defines how a client application (like an IDE or desktop shell) communicates with a local/remote AI coordinator and specialized context servers.
- **Architecture:** It splits the ecosystem into three parts:
  1. **MCP Hosts:** Applications that orchestrate the LLM and UI (e.g., Claude Desktop, Cursor, or our custom Tauri app).
  2. **MCP Clients:** Client instances inside the host that connect to MCP Servers.
  3. **MCP Servers:** Lightweight services that expose three things to the client: **Resources** (readable data like file contents or database tables), **Prompts** (predefined templates), and **Tools** (executable functions like compiling code or searching the web).
- **Advantage over Custom APIs:** Before MCP, each app built custom tool integrations using ad-hoc JSON schemas. MCP standardizes this over JSON-RPC 2.0. An LLM can seamlessly query tools across multiple independent servers (e.g., one server reading local git diffs, another querying a postgres DB) using a unified protocol. It abstracts tool transport and schema discovery."

### 7. Walk me through the implementation of a custom local MCP Server in TypeScript using the `@modelcontextprotocol/sdk`.
**Expert Answer:**
"Implementing a custom local MCP server in TypeScript involves:
1. **Initialization:** Install `@modelcontextprotocol/sdk` and initialize the Server class:
   ```typescript
   import { Server } from "@modelcontextprotocol/sdk/server/index.js";
   import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

   const server = new Server({
     name: "local-file-indexer",
     version: "1.0.0"
   }, {
     capabilities: { tools: {} }
   });
   ```
2. **Registering Tools:** Define the tools that the LLM can invoke. For example, a tool to search local code:
   ```typescript
   server.setRequestHandler(ListToolsRequestSchema, async () => ({
     tools: [{
       name: "search_code",
       description: "Search local files using semantic query",
       inputSchema: {
         type: "object",
         properties: {
           query: { type: "string" }
         },
         required: ["query"]
       }
     }]
   }));
   ```
3. **Handling Executions:** Write the execution handler mapping tool names to actual functions (like querying ChromaDB):
   ```typescript
   server.setRequestHandler(CallToolRequestSchema, async (request) => {
     if (request.params.name === "search_code") {
       const results = await localSearch(request.params.arguments.query);
       return {
         content: [{ type: "text", text: JSON.stringify(results) }]
       };
     }
     throw new Error("Tool not found");
   });
   ```
4. **Start Server:** Connect the server to a transport protocol, typically standard I/O (Stdio) for local desktop clients:
   ```typescript
   const transport = new StdioServerTransport();
   await server.connect(transport);
   ```
The Tauri desktop app starts this script as a sidecar process, connecting to its stdin/stdout to command it."

---

## Part 4: Desktop Architecture & Tauri v2

### 8. Contrast Tauri v2's architecture with Electron. Why is Tauri preferred for resource-constrained local AI applications?
**Expert Answer:**
"Tauri v2 offers massive efficiency benefits over Electron for desktop applications:
- **Resource Consumption:** Electron bundles the entire Chromium browser engine and Node.js runtime inside every application bundle, leading to huge package sizes (150MB+) and high baseline RAM usage (100MB+ idle). Tauri uses the OS's native Webview (WebView2 on Windows, WebKit on macOS/Linux) and a Rust backend, resulting in tiny package sizes (often <15MB) and extremely low RAM footprint (typically 30-40MB).
- **Security Model:** Electron runs Node.js directly in the renderer process (unless sandbox is enabled), which creates severe security vulnerabilities if third-party code is executed. Tauri enforces strict isolation. The frontend has absolutely no direct access to the operating system; instead, it must communicate with the Rust backend via a secure IPC (Inter-Process Communication) bridge using custom commands.
- **AI Processing:** Rust is compile-time safe and close to the metal, making it an excellent host language for loading local quantized models (via llama.cpp bindings or Wasm runtimes) or executing high-speed file system operations, which Node.js struggles to do with equivalent efficiency."

### 9. In Tauri v2, how do you handle bidirectional communication, specifically passing an active text stream from Rust to the Webview?
**Expert Answer:**
"To stream text dynamically (like LLM outputs) from the Rust core to the React frontend, I use Tauri's event emission system rather than a standard single-return Command:
1. **Rust Side:** I define an asynchronous command that takes the window handle and emits events:
   ```rust
   #[tauri::command]
   async fn start_ai_generation(window: tauri::Window, prompt: String) -> Result<(), String> {
       let mut stream = call_llm_stream(prompt).await?;
       while let Some(token) = stream.next().await {
           // Emit event to the frontend webview
           window.emit("generation-token", token).map_err(|e| e.to_string())?;
       }
       window.emit("generation-complete", ()).map_err(|e| e.to_string())?;
       Ok(())
   }
   ```
2. **Frontend Side (React):** I listen to these events using Tauri's `@tauri-apps/api/event` module:
   ```typescript
   import { listen } from '@tauri-apps/api/event';

   useEffect(() => {
     let unlistenToken: () => void;
     
     async function setupListeners() {
       unlistenToken = await listen<string>('generation-token', (event) => {
         const token = event.payload;
         updateChatStore(token); // Append to state
       });
     }
     setupListeners();
     return () => {
       if (unlistenToken) unlistenToken();
     };
   }, []);
   ```
This provides a non-blocking, asynchronous channel for real-time text streaming."

---

## Part 5: Advanced Testing (Vitest, Playwright, Stryker)

### 10. How does Mutation Testing with Stryker differ from standard code coverage, and why is it crucial for verifying AI/LLM integration logic?
**Expert Answer:**
"Standard code coverage (line/branch coverage) only tells you that a line of code was executed during a test; it does **not** tell you if the test actually asserts the correctness of that line. An assertion-free test can easily achieve 100% coverage.
- **Mutation Testing:** Stryker works by introducing tiny bugs ('mutants') directly into your compiled code (e.g., changing a `>` to `>=`, swapping `+` for `-`, or deleting a function call) and running the test suite. If the tests fail, the mutant is **killed** (good). If the tests pass, the mutant **survived** (bad), indicating your test suite failed to catch a breaking change.
- **Importance in AI Logic:** LLM integration code relies on complex parser logic, tool schema translations, and state transitions. For example, if a mutant alters how prompt context is sliced, standard tests might still pass because the LLM still returns a response, but the quality of the response is degraded. Mutation testing forces you to write deep, semantic assertions on the structures of prompt payloads, tools parameters, and vector search thresholds, ensuring the application handles structural edge cases correctly."
