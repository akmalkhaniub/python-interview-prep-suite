# 02 - Node.js & Backend APIs

## Node.js Core

### 1. Explain the Node.js Event Loop.
**Answer:**
Node.js is single-threaded but uses the Event Loop to handle asynchronous operations.
- **Phases:** Timers (setTimeout), I/O Callbacks, Idle/Prepare, Poll (new I/O events), Check (setImmediate), Close Callbacks.
- **Mechanism:** It offloads blocking tasks (like DB queries or file reads) to the system kernel or a thread pool (libuv) and continues executing code. When the task is done, the callback is added to the queue to be executed in the next cycle.

### 2. Difference between `process.nextTick()` and `setImmediate()`?
**Answer:**
- **process.nextTick():** Executes immediately after the current operation finishes, *before* the event loop continues to the next phase.
- **setImmediate():** Executes in the "Check" phase of the event loop (after the "Poll" phase).

### 3. How do you handle CPU-intensive tasks in Node.js?
**Answer:**
Since Node is single-threaded, CPU-bound tasks block the event loop.
- **Fixes:** Use **Worker Threads** (built-in module), offload to a microservice in a different language (Go/Python), or use a child process.

## API Design & Security

### 4. What are REST vs. GraphQL?
**Answer:**
- **REST:** Resource-based. Multiple endpoints. Can suffer from over-fetching or under-fetching.
- **GraphQL:** Query-based. Single endpoint. The client specifies exactly what data it needs. Great for complex, nested data relationships.

### 5. How do you secure a Node.js API?
**Answer:**
- **Authentication:** Use JWT or Sessions.
- **Input Validation:** Use libraries like Joi or Zod.
- **Rate Limiting:** Prevent Brute-force/DoS attacks.
- **Helmet.js:** Set various HTTP headers for security.
- **CORS:** Restrict which domains can access your API.
- **Environment Variables:** Never hardcode secrets.

### 6. Explain the concept of "Middleware" in Express.
**Answer:**
Functions that have access to the Request object (`req`), the Response object (`res`), and the `next` function. They can execute code, modify `req`/`res`, and end the request-response cycle or pass control to the next middleware.
