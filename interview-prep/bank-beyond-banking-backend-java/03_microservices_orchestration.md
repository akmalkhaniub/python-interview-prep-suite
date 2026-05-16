# Microservices Orchestration (Java/Spring Context)

This guide focuses on the "Bank-Grade" microservices requirements: Java expertise, orchestration, and observability.

---

## 1. Java Microservices Architecture
*   **The Stack:** Spring Boot 3.x, Spring Cloud, Hibernate/JPA.
*   **Service Boundaries:** Domain-Driven Design (DDD). Each Beyond Banking vertical (Travel, eSIM, E-commerce) should be a separate bounded context with its own database to ensure isolation.
*   **API Gateway:** Using **Spring Cloud Gateway** for centralized authentication (OIDC), rate limiting, and request routing to internal microservices.

## 2. Distributed Systems Patterns (The "Must-Haves")
*   **Saga Pattern (Orchestration):** 
    *   *Problem:* A "Travel Booking" involves three microservices: `TravelService`, `PaymentService`, and `NotificationService`. We can't use a distributed 2-Phase Commit (2PC) due to latency.
    *   *Solution:* The `TravelService` acts as the Orchestrator. If `PaymentService` fails, the `TravelService` triggers a "Compensating Transaction" in the `TravelProvider API` to cancel the pending booking.
*   **Transactional Outbox:** 
    *   Ensuring that a database update and a message (to Kafka/NATS) are atomic. Write the event to an `outbox` table in the same transaction as the business data, then a separate process picks it up for delivery.
*   **Idempotency Key:** Every request from the super-app must include a `request_id`. The backend checks if this ID has already been processed to prevent double-charging on retries.

## 3. Observability & Tracing
*   **Correlation IDs:** Passing a `X-Correlation-ID` header across all microservice calls.
*   **Distributed Tracing:** Using **OpenTelemetry** or **Zipkin/Jaeger**. 
*   **Talking Point:** "In a bank, logs are not enough. We need full distributed tracing to understand exactly where a transaction hung—was it our Payment service, or the partner's Travel API?"

## 4. Resilience Patterns
*   **Circuit Breaker:** If a partner API (e.g., an eSIM provider) is timing out, the circuit "opens" and we return a cached response or a "Service Temporarily Unavailable" message immediately, saving our system's resources.
*   **Bulkheading:** Isolating resources for different partners. A surge in "Travel" traffic shouldn't exhaust the thread pool used for "Core Payments."
*   **Retry with Backoff:** Automatically retrying transient failures (503, 429) using an exponential backoff strategy.

---

## 5. Security Architecture
*   **OAuth2 + OIDC:** Using **Spring Security** with an Authorization Server (e.g., Keycloak or Azure Entra ID).
*   **MTLS (Mutual TLS):** Ensuring that communication between microservices and between the Bank and Partners is authenticated at the network layer.
*   **Token Relay:** Securely passing the user's JWT from the Gateway to the downstream microservices.
