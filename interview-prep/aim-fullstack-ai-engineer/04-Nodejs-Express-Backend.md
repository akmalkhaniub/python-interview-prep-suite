# Backend: Node.js, Express & TypeScript
**Focus:** Event Loop, Scalable API Design, Error Handling, Validation

---

## Section A: Node.js Architecture

### Q1: The Node.js Event Loop
**Question:** How does the Node.js Event Loop handle asynchronous operations, and how do you prevent blocking it in a production backend?

**Expected Answer:**
Node.js is fundamentally single-threaded, operating on a non-blocking, asynchronous event-driven architecture. When a Node application receives an I/O request (like a database query or an external API call), the main thread does not wait for it to finish. Instead, it offloads the heavy lifting to the OS kernel or the `libuv` thread pool and registers a callback. The Event Loop continuously monitors these background operations, and when one completes, it pushes the callback onto the callback queue to be executed by the main thread.

Because there is only one main thread, executing CPU-intensive tasks directly in an Express route—such as complex cryptographic hashing, deep JSON parsing of massive payloads, or synchronous file reads (`fs.readFileSync`)—will "block the event loop." While the thread is calculating the hash, the server cannot process any other incoming HTTP requests, bringing the entire application to a halt. To prevent this, CPU-bound tasks must be strictly offloaded to dedicated Worker Threads (`worker_threads` module) or external microservices via a message queue.

---

### Q2: Scalable Express & TypeScript Architecture
**Question:** Architect a scalable RESTful API using Express and TypeScript. How do you structure routing and middleware?

**Expected Answer:**
A scalable Express application must eschew massive, monolithic `app.js` files in favor of a strict modular architecture. I employ the **Controller-Service-Repository** pattern. 

The **Router** layer (`routes/patient.routes.ts`) simply maps HTTP verbs and paths to specific Controllers. The **Controller** layer handles HTTP-specific logic: parsing `req.body`, returning HTTP status codes (`res.status(200)`), and nothing else. The Controller delegates the actual business logic to the **Service** layer (`PatientService.ts`). The Service executes business rules and relies on the **Repository** layer (or raw ORM models) to interact with the database. By strictly decoupling HTTP logic from business logic, I can easily reuse the `PatientService` in a background cron job or a WebSocket handler without faking an HTTP request object. I enforce this architecture using strict TypeScript interfaces for all layer boundaries.

---

## Section B: Robust API Operations

### Q3: Error Handling and Structured Logging
**Question:** How do you implement robust error handling and structured logging in a Node.js Express application?

**Expected Answer:**
Unhandled promise rejections and silent failures are unacceptable in production. In Express, I build a centralized Error Handling Middleware function (`app.use((err, req, res, next) => {...})`) that acts as a catch-all at the end of the routing stack. I define custom TypeScript Error classes (e.g., `ValidationError`, `DatabaseError`) that extend the base `Error` class and include specific HTTP status codes. When a Controller encounters an issue, it simply calls `next(new ValidationError('Invalid Date'))`, and the centralized handler formats a standardized JSON response for the frontend.

For logging, `console.log` is insufficient because it lacks context and is difficult to parse in log aggregators (like AWS CloudWatch). I use a structured logging library like **Winston** or **Pino**. Every log entry is output in JSON format, capturing critical metadata such as the `request_id`, `user_id`, log level, and a stack trace if an error occurs. This ensures that when debugging an incident in production, we can instantly filter and trace a single user's request lifecycle across the entire system.

---

### Q4: Input Validation Strategy
**Question:** What is your strategy for validating incoming request data in an Express API, specifically in a compliance-heavy domain?

**Expected Answer:**
In a healthcare or payroll platform, trusting client input is a massive security vulnerability. All incoming payloads must be strictly validated at the API boundary before they ever reach the business logic.

I use a robust schema validation library like **Zod** or **Joi**. For a POST request to create a new care shift, I define a strict Zod schema enforcing data types, string lengths, and specific formats (e.g., ensuring a date string is actually ISO-8601 compliant and in the future). I implement an Express validation middleware that intercepts the request, runs `schema.parse(req.body)`, and instantly returns a `400 Bad Request` with a detailed array of validation errors if the payload is malformed. Crucially, Zod automatically infers TypeScript types from the schema, ensuring the validated `req.body` is strongly typed when it finally reaches the Controller, guaranteeing end-to-end data safety.

---

### Q5: Database Connection Pooling
**Question:** How do you manage database connections and connection pooling in a Node.js application to prevent crashes under high load?

**Expected Answer:**
Creating a new PostgreSQL connection for every incoming HTTP request introduces massive latency overhead and will quickly exhaust the database's maximum connection limit, crashing the system under heavy load.

Instead, I configure a **Connection Pool** using tools like `pg` (node-postgres) or an ORM like Prisma/TypeORM. When the Node application starts, it initializes a pool of reusable database connections (e.g., `max: 20`). When a route executes a query, it "borrows" a connection from the pool, executes the SQL, and immediately releases it back to the pool. To prevent zombie connections, I configure strict timeouts (`idleTimeoutMillis`, `connectionTimeoutMillis`). If the application scales to 50 concurrent ECS containers, each with a pool of 20, we risk overwhelming Postgres. In that high-scale scenario, I would deploy a dedicated connection bouncer like **PgBouncer** between the Node applications and the database to securely multiplex thousands of client connections onto a small number of actual database backend connections.
