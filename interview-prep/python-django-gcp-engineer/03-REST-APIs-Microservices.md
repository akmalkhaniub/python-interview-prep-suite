# RESTful APIs & Microservices Architecture
**Focus:** REST Principles, API Design, Microservices, Distributed Systems

---

## Section A: RESTful Architecture & API Design

### Q1: Principles of REST
**Question:** What are the fundamental constraints that make an API truly RESTful?

**Expected Answer:**
REpresentational State Transfer (REST) is an architectural style defined by several strict constraints. First is **Client-Server Separation**, ensuring the UI and data storage evolve independently. Second is **Statelessness**; the server must not store any client context between requests. Every single HTTP request must contain all the information (like auth tokens) necessary to execute it, allowing horizontal scaling without sticky sessions. 

Third is **Cacheability**, requiring responses to implicitly or explicitly define themselves as cacheable or not. Fourth is a **Uniform Interface**, meaning the API must use standard HTTP verbs (`GET` for reading, `POST` for creation, `PUT`/`PATCH` for updates, `DELETE` for removal) and resource-based URIs (`/users/123/orders`), rather than action-based RPC URIs (`/getOrdersForUser`). A truly mature REST API also implements HATEOAS (Hypermedia as the Engine of Application State), returning hyperlinks in the response payload to guide the client through available state transitions dynamically.

---

### Q2: API Versioning Strategies
**Question:** How do you approach versioning a public REST API, and what are the trade-offs of the different methods?

**Expected Answer:**
Versioning is critical to prevent breaking downstream clients as the application evolves. There are three primary strategies. **URI Versioning** (e.g., `/api/v1/users`) is the most explicit and easiest to implement. It routes traffic clearly, but purists argue it violates REST principles because the URI should represent the resource, not the version. 

**Header Versioning**, specifically using a custom header like `X-API-Version: 2.0`, keeps the URIs clean but makes the API harder to explore in simple browser tests without specialized tools like Postman. Finally, **Content Negotiation** via the `Accept` header (e.g., `Accept: application/vnd.company.v2+json`) is the most technically REST-compliant method, allowing clients to request specific payload structures. However, it is highly complex to implement and document. I generally advocate for URI versioning (`/v1/`) due to its unmatched developer experience, simplicity, and caching reliability at the CDN layer.

---

## Section B: Microservices & Distributed Systems

### Q3: Monolith to Microservices
**Question:** Describe your strategy for decomposing a monolithic Django application into a microservices architecture.

**Expected Answer:**
Decomposing a monolith should never be a "big bang" rewrite; it must be an incremental, risk-mitigated process using the **Strangler Fig Pattern**. First, I identify natural domain boundaries within the monolith using Domain-Driven Design (DDD) principles—for example, separating the "Billing" domain from the "User Profile" domain. 

I start by extracting the least entangled, highest-value service. We build the new microservice alongside the monolith and deploy an API Gateway in front of both. The Gateway routes traffic for the specific isolated domain to the new microservice, while all legacy traffic falls back to the monolith. The hardest challenge is data. Microservices must own their own data stores; a shared database is just a distributed monolith. We must split the database schemas, migrating the billing tables to the new service and refactoring the monolith to query the new billing API over HTTP/gRPC instead of executing direct SQL joins.

---

### Q4: Inter-Service Communication & Reliability
**Question:** In a microservices architecture, how do you ensure services communicate securely and reliably when the network is inherently unreliable?

**Expected Answer:**
Relying purely on synchronous HTTP calls between microservices creates a fragile, tightly coupled system where one degraded service causes cascading timeouts across the entire platform. To ensure reliability, communication must heavily leverage **Asynchronous Event-Driven Architecture**. Services should publish domain events (e.g., `UserCreated`) to a message broker like Google Cloud Pub/Sub or Apache Kafka. Downstream services consume these events at their own pace, providing extreme fault tolerance and decoupling.

For necessary synchronous calls, we must embrace the philosophy of "designing for failure." Every HTTP/gRPC client must implement strict **Timeouts** to prevent hanging threads. We must wrap outbound calls in the **Circuit Breaker Pattern**; if a downstream service fails repeatedly, the circuit trips open, immediately returning an error or fallback cache instead of waiting for a timeout, giving the failing service time to recover. Finally, to handle transient network blips, we implement exponential backoff and retry logic.

---

### Q5: Distributed Transactions (Saga Pattern)
**Question:** How do you handle a transaction that spans multiple microservices, such as processing an order, charging a card, and updating inventory?

**Expected Answer:**
In a monolithic Django application, this is solved by a simple database transaction with `atomic()`, rolling back everything if an error occurs. In microservices, utilizing two-phase commits (2PC) across distributed databases introduces massive latency and locking issues. The modern solution is the **Saga Pattern**.

A Saga is a sequence of local transactions. When a service completes its local transaction (e.g., the Order Service creates an order), it publishes an event. The next service (Billing) consumes the event, charges the card, and publishes a success event. If a failure occurs later in the chain (e.g., the Inventory Service realizes the item is out of stock), the system cannot issue a standard SQL rollback. Instead, it must fire a chain of **Compensating Transactions** backward. The Inventory failure event triggers the Billing service to execute a "Refund" logic, and then triggers the Order service to mark the order as "Cancelled." This ensures eventual consistency across the distributed system without requiring distributed database locks.
