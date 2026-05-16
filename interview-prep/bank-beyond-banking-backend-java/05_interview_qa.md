# Interview Q&A: Bank Backend Engineer (Java)

### Q1: Distributed Systems
**Question:** We are building a Travel booking feature. The user pays, but the Travel Partner's API times out. How do you handle this to ensure the user isn't double-charged or left with a failed booking?
**Answer:** This is a classic "State Uncertainty" problem. I would implement an **Idempotent Retry Policy** and a **Verification Step**.
1.  **Idempotency:** The request to the Partner includes a unique `booking_reference_id`.
2.  **Verification:** If a timeout occurs, instead of retrying immediately, my service calls a "Get Booking Status" endpoint on the Partner's side using that ID.
3.  **Branching:** If the partner says "Booking Confirmed," we update our DB and finish. If they say "Not Found," we retry the booking. 
This prevents the "Double Booking" problem and ensures transactional integrity even during network failures.

### Q2: Java / Microservices
**Question:** How do you prevent a single slow microservice (like a third-party marketplace) from slowing down the entire banking application?
**Answer:** I use the **Circuit Breaker** and **Bulkhead** patterns.
- **Circuit Breaker (Resilience4j):** If the marketplace API has high latency or error rates, the circuit opens, and we return a fallback response (e.g., "Marketplace currently offline") instead of waiting for a timeout.
- **Bulkhead:** I isolate the thread pools for Beyond Banking services. This ensures that even if the Marketplace service is consuming all its allocated threads, the core "Fund Transfer" service has its own dedicated threads and remains responsive.

### Q3: Transactional Integrity
**Question:** What is the "Saga Pattern" and why is it preferred over Two-Phase Commit (2PC) in microservices?
**Answer:** 2PC is a blocking protocol that requires all participating databases to be locked until the transaction finishes, which doesn't scale in a distributed environment. **Saga** is a sequence of local transactions. Each step updates its own database and triggers the next step. If a step fails, the Saga executes **Compensating Transactions** (rollbacks) for all previous steps. This provides "Eventual Consistency" without the performance bottleneck of distributed locks.

### Q4: Security
**Question:** A partner asks us to send customer account numbers in an API request. How do you respond from a security perspective?
**Answer:** I would strongly advise against sending raw PII or account numbers. Instead, I would propose a **Tokenization** or **Reference ID** approach. We send a unique, non-sensitive `partner_user_id` that is mapped to the account in our secure internal system. This aligns with **Zero-Trust** principles and ensures that if the partner's system is compromised, our customer's core banking data remains protected.

### Q5: Engineering Rigor
**Question:** What does "Observability" mean to you in a banking context?
**Answer:** It's the difference between "The system is up" and "The system is working correctly." To me, it means:
1.  **Distributed Tracing:** Seeing the full path of a transaction across 5 microservices.
2.  **Structured Logging:** Every log entry has a `transaction_id` and `user_id`.
3.  **Metrics & Alerts:** Setting up alerts for "Anomalous Rejection Rates"—if 10% of bookings are failing, something is wrong even if the server is technically "up."

---

### Pro-Tips for the Bank Interview:
- **Correctness Over Speed:** Always emphasize that you would rather a transaction fail gracefully than succeed incorrectly.
- **Java 17/21:** Be ready to discuss modern Java features (Records, Sealed Classes, Virtual Threads) and how they improve microservices performance.
- **Compliance:** Mention your understanding of **Data Residency** and **Audit Trails**—banks are heavily regulated.
