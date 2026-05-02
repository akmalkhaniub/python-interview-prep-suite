# 01 - Microservices & Distributed Systems

## Microservices Architecture

### 1. What are the core benefits and challenges of Microservices?
**Answer:**
- **Benefits:** Independent scaling, technology flexibility, fault isolation, and faster deployment cycles for individual services.
- **Challenges:** Increased operational complexity, data consistency issues (CAP theorem), difficult debugging (distributed tracing needed), and network latency.

### 2. Explain the "API Gateway" pattern.
**Answer:**
A single entry point for all client requests. It handles:
- **Routing:** Directing requests to the correct microservice.
- **Authentication/Authorization:** Centralizing security checks.
- **Rate Limiting:** Protecting services from over-use.
- **Protocol Translation:** Converting between HTTP and gRPC, for example.

### 3. What is the "Circuit Breaker" pattern?
**Answer:**
A mechanism to prevent a failing service from causing a cascade of failures across the system. 
- **Closed:** Normal operation.
- **Open:** Service has failed; requests are immediately rejected or routed to a fallback.
- **Half-Open:** Periodically checking if the service has recovered.

## Distributed Systems Concepts

### 4. What is the CAP Theorem?
**Answer:**
A principle stating that a distributed system can only provide two out of three guarantees:
- **Consistency:** All nodes see the same data at the same time.
- **Availability:** Every request receives a response (even if it's stale data).
- **Partition Tolerance:** The system continues to operate despite network failures.
- **Note:** In reality, you must choose Partition Tolerance ($P$), meaning you trade off between $C$ and $A$.

### 5. How do you handle "Distributed Transactions" (Saga Pattern)?
**Answer:**
Since traditional ACID transactions don't work across microservices, the Saga pattern is used:
- A sequence of local transactions where each one updates its own database and triggers the next one.
- If one step fails, "Compensating Transactions" are executed to undo the previous steps.

### 6. Importance of "Distributed Tracing" (Jaeger/Zipkin)?
**Answer:**
Allows you to follow a single request as it travels through multiple microservices. Essential for identifying performance bottlenecks and debugging complex failures in a distributed environment.
