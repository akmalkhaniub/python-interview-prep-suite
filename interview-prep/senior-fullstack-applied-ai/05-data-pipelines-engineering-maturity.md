# 05 - Data Pipelines & SDLC Maturity

## Data Engineering

### 1. What is an "ETL" (Extract, Transform, Load) process?
**Answer:**
- **Extract:** Pulling data from various sources (APIs, DBs).
- **Transform:** Cleaning, normalizing, and calculating new values.
- **Load:** Saving the data into a target system (Data Warehouse, another DB).
- **In Python:** Often using libraries like `Pandas`, `Dask`, or custom scripts.

### 2. Best practices for "Data Integrity" in pipelines?
**Answer:**
- **Validation:** Checking data types and ranges at every step.
- **Idempotency:** Ensuring that running a pipeline twice doesn't duplicate data.
- **Logging:** Tracking how much data was processed and where errors occurred.
- **Atomicity:** Using transactions so that a pipeline fails cleanly if an error occurs.

### 3. How to design a "Scalable Data Flow"?
**Answer:**
Using Message Queues (Kafka, RabbitMQ) to decouple the extraction from the processing. This allows each part of the pipeline to scale independently.

## Engineering Maturity

### 4. Improving "SDLC Maturity" in a team?
**Answer:**
- **Code Review:** Establishing a constructive and rigorous review culture.
- **CI/CD:** Automating tests and deployments to reduce manual errors.
- **Documentation:** Encouraging ADRs and living documentation.
- **Testing:** Moving from "no tests" to unit, integration, and E2E coverage.

### 5. Why is "Testing" crucial in a low-structure environment?
**Answer:**
It acts as the "Saftey Net" that allows you to move fast and refactor without fear. In a small team without dedicated QA, the code *must* test itself.

### 6. Importance of "Stakeholder Communication"?
**Answer:**
Ensuring that technical decisions align with business goals. Regularly reporting on progress, risks, and trade-offs in a way that non-technical people can understand.
