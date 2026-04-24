# Backend Development & Node.js

**Focus:** Node.js architecture, Express.js vs NestJS, asynchronous programming, event loop

---

### Q1: The Node.js Event Loop
**Question:** Node.js is single-threaded, yet it handles highly concurrent workloads. Explain how the Event Loop makes this possible.

**Expected Answer:**
- **Single Threaded**: The main V8 JavaScript thread is single-threaded.
- **Event Loop**: It continuously checks for pending events or tasks and executes their callbacks.
- **Libuv & Worker Pool**: I/O operations (file system, network, crypto) are offloaded to `libuv`, which manages a pool of C++ worker threads.
- **Phases**: The event loop has phases (Timers, Pending Callbacks, Poll, Check, Close Callbacks).
- **Non-blocking I/O**: When a database query is made, Node offloads it. The event loop continues handling other requests. When the DB responds, the callback is pushed to the queue and executed.
- **Caution**: CPU-bound tasks (e.g., heavy image processing, complex JSON parsing) *will* block the event loop. Use Worker Threads for these.

---

### Q2: Express.js vs. NestJS
**Question:** You have experience with both Express and NestJS. When would you choose one over the other for a new project?

**Expected Answer:**
- **Express.js**:
  - **Pros**: Minimalist, unopinionated, massive ecosystem, fast to set up for simple APIs or microservices.
  - **Cons**: Lacks structure. In large teams, this leads to messy, inconsistent codebases (spaghetti code) unless strict conventions are enforced manually.
- **NestJS**:
  - **Pros**: Highly opinionated, uses TypeScript by default, heavily inspired by Angular. Provides built-in architecture (Modules, Controllers, Providers/Services, Guards, Interceptors). Excellent for large, enterprise-grade applications with multiple developers.
  - **Cons**: Steeper learning curve, heavier boilerplate for small tasks.
- **Verdict**: Use Express for small, fast microservices or prototypes. Use NestJS for large, long-term enterprise applications requiring high maintainability.

---

### Q3: Handling Async Errors
**Question:** How do you handle errors in asynchronous Node.js code, particularly in Express.js?

**Expected Answer:**
- **Promises/Async-Await**: Wrap `await` calls in `try/catch` blocks.
- **Express specific**: In Express 4, if an async route handler throws an error, it will crash the app (Unhandled Promise Rejection) unless caught and passed to `next(err)`.
- **Solution 1 (Express 4)**: Use a wrapper function like `express-async-handler` to automatically catch errors and pass them to the global error handler.
- **Solution 2 (Express 5)**: Natively supports returning Promises in route handlers and handles rejections automatically.
- **Global Error Handler**: A 4-arity middleware `(err, req, res, next)` defined at the end of the pipeline to catch all errors, log them, and return a standardized JSON response.

```javascript
// The wrapper approach for Express 4
const asyncHandler = fn => (req, res, next) => {
  return Promise.resolve(fn(req, res, next)).catch(next);
};
```

---

### Q4: Middleware in Node.js
**Question:** What is middleware in Express/NestJS, and what are some common use cases?

**Expected Answer:**
- **Definition**: Functions that have access to the request (`req`), response (`res`), and the `next` middleware function in the application's request-response cycle.
- **Flow**: They can execute code, modify req/res objects, end the response cycle, or call `next()`.
- **Common Use Cases**:
  - **Authentication/Authorization**: Verifying JWT tokens before allowing access to a route.
  - **Body Parsing**: Parsing incoming JSON payloads (`express.json()`).
  - **Logging**: Logging request details (method, URL, IP) using tools like Morgan.
  - **CORS**: Handling Cross-Origin Resource Sharing headers.
  - **Error Handling**: Global error catching.

---

### Q5: Memory Leaks in Node.js
**Question:** How would you identify and fix a memory leak in a Node.js application running in production?

**Expected Answer:**
- **Symptoms**: Memory usage steadily increasing over time until the application crashes (OOM - Out of Memory) and restarts.
- **Common Causes**: Global variables, unclosed database connections, uncleared timers/intervals, extensive use of closures holding onto large objects.
- **Identification**:
  - Use monitoring tools (PM2, Datadog, New Relic) to observe memory trends.
  - Take Heap Snapshots (`node --inspect`) before and after load testing.
  - Use Chrome DevTools to compare heap snapshots and find objects that aren't being garbage collected.
- **Fixing**: Remove references to unused objects, ensure intervals are cleared, and avoid accumulating data in global arrays.
