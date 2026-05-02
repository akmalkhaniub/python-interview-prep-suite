# 02 - Backend & Microservices

## Backend Patterns

### 1. What is a "Monolithic" vs. "Microservices" Architecture?
**Answer:**
- **Monolith:** All code in one codebase, one deployment unit. Pros: Simpler, faster initial development. Cons: Harder to scale parts independently, "all-or-nothing" failures.
- **Microservices:** Application split into small, independent services communicating via APIs. Pros: Scalability, technology flexibility (Node for one, Python for another). Cons: Complexity in deployment, networking, and data consistency.

### 2. How do you design a RESTful API?
**Answer:**
- **Resources:** Use nouns, not verbs (e.g., `/users`, not `/getUsers`).
- **HTTP Verbs:** GET (read), POST (create), PUT (update), DELETE (delete).
- **Status Codes:** 200 (OK), 201 (Created), 400 (Bad Request), 401 (Unauthorized), 404 (Not Found), 500 (Server Error).
- **Versioning:** e.g., `/api/v1/resource`.

### 3. What is an API Gateway?
**Answer:**
A single entry point for all client requests. It handles routing, authentication, rate limiting, and potentially response aggregation from multiple microservices.

## Language Specifics

### 4. Node.js: What is the "Module Pattern"?
**Answer:**
Using `require/module.exports` (CommonJS) or `import/export` (ESM) to encapsulate logic into separate files, keeping the global scope clean.

### 5. Python: What are Decorators?
**Answer:**
Functions that modify the behavior of another function without changing its source code. Often used for logging, auth checks, or timing (e.g., `@login_required`).

### 6. How do you handle "CORS" (Cross-Origin Resource Sharing)?
**Answer:**
A security feature where the browser prevents a frontend on one domain from making requests to a backend on another unless the backend explicitly allows it via HTTP headers (`Access-Control-Allow-Origin`).
