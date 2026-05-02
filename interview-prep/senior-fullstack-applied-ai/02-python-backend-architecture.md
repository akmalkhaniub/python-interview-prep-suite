# 02 - Python Backend & Architecture

## Python in Production

### 1. Best practices for scaling a Python backend?
**Answer:**
- **Asynchronous IO:** Using `asyncio` or frameworks like FastAPI to handle high concurrency.
- **Worker Queues:** Offloading heavy tasks (AI processing, data pipelines) to Celery/Redis.
- **Microservices vs. Modular Monolith:** Deciding when to split logic into separate services based on team size and domain boundaries.
- **Stateless Design:** Ensuring any instance can handle any request, facilitating horizontal scaling.

### 2. How do you design for "Maintainability" in a growing codebase?
**Answer:**
- **Type Hinting:** Using Python's typing system for clarity and IDE support.
- **Domain-Driven Design (DDD):** Organizing code around business domains rather than technical layers.
- **Testing:** Comprehensive unit and integration tests.
- **Clean Code:** Following PEP 8 and using tools like Black, Flake8, and Mypy.

### 3. Database Modeling Trade-offs?
**Answer:**
- **Relational (SQL):** Best for structured data and complex joins.
- **Non-Relational (NoSQL):** Best for unstructured data (like AI model outputs) and massive scale.
- **Migrations:** Using tools like Alembic to manage schema changes safely.

## System Architecture

### 4. How do you handle "Security" in a full-stack app?
**Answer:**
- **Authentication:** OAuth2/JWT best practices.
- **Validation:** Never trusting client-side input; rigorous backend validation.
- **ORM Security:** Preventing SQL injection through parameterization.
- **Secrets Management:** Using env variables or secret managers for API keys.

### 5. What is "Idempotency" and why use it?
**Answer:**
Ensuring that multiple identical requests have the same effect as a single one (e.g., retrying a payment). Use idempotency keys in headers.

### 6. Importance of "Observability" (Logging & Monitoring)?
**Answer:**
- **Logs:** Centralized logging with searchability (ELK, Datadog).
- **Metrics:** Tracking performance (p99 latency) and business health.
- **Tracing:** Visualizing a request as it moves through multiple services.
