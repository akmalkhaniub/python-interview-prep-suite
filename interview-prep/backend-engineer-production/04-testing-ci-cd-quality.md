# 04 - Testing, CI/CD & Code Quality

## Testing Strategy

### 1. What is the "Testing Pyramid"?
**Answer:**
- **Unit Tests (Base):** Test individual functions/classes. Fast and numerous.
- **Integration Tests (Middle):** Test interaction between components (e.g., DB, APIs).
- **E2E/UI Tests (Top):** Test the entire user flow. Slow and expensive.
- **Principle:** You should have more unit tests than E2E tests.

### 2. What is TDD (Test-Driven Development)?
**Answer:**
A workflow: **Red** (Write a failing test) -> **Green** (Write the minimum code to pass) -> **Refactor** (Improve the code).

### 3. Difference between "Stubbing" and "Mocking"?
**Answer:**
- **Stub:** Returns fixed data to a call. Focuses on the "state."
- **Mock:** Expects certain calls to happen. Focuses on the "behavior."

## CI/CD & Quality

### 4. What is a CI/CD Pipeline?
**Answer:**
- **CI (Continuous Integration):** Automates merging code and running tests on every push.
- **CD (Continuous Deployment/Delivery):** Automates the release process to staging/production.

### 5. Importance of "Code Reviews"?
**Answer:**
- Catch bugs and architectural flaws.
- Ensure consistency in coding style.
- Share knowledge across the team.
- Provide a "second pair of eyes" on complex logic.

### 6. What is "Observability" (Logs, Metrics, Traces)?
**Answer:**
- **Logs:** What happened? (e.g., Error stack traces).
- **Metrics:** How is the system performing? (e.g., CPU, Latency, Throughput).
- **Traces:** How did a request travel through the system? (Essential for microservices).
