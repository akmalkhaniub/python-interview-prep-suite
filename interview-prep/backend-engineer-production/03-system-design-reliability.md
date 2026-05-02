# 03 - System Design & Reliability

## Scalability

### 1. Difference between "Vertical" and "Horizontal" scaling?
**Answer:**
- **Vertical:** Adding more power (CPU/RAM) to one server. Limited by hardware.
- **Horizontal:** Adding more servers (nodes). Practically unlimited but requires a Load Balancer and stateless application design.

### 2. What is a "Load Balancer"?
**Answer:**
A device/software that distributes incoming traffic across multiple servers. Algorithms include Round Robin, Least Connections, or IP Hash.

### 3. Importance of "Caching" (Redis/Memcached)?
**Answer:**
Storing expensive-to-calculate data in memory for fast retrieval.
- **Cache Aside:** Check cache -> If miss, fetch from DB -> Update cache.
- **Write Through:** Update cache and DB simultaneously.

## Reliability

### 4. What is a "Message Queue" (Kafka/RabbitMQ)?
**Answer:**
A system to decouple services. Instead of Service A calling Service B synchronously, it pushes a message to a queue. Service B processes it when ready.
- **Benefit:** Handles traffic spikes, increases reliability, and enables asynchronous processing (e.g., sending emails).

### 5. Explain "Microservices" vs. "Monolith."
**Answer:**
- **Monolith:** All logic in one repo/process. Faster to start, simpler to test initially.
- **Microservices:** Logic split into small, independent services. Enables independent scaling, technology choice, and fault isolation, but adds complexity in networking and consistency.

### 6. What is a "Circuit Breaker" pattern?
**Answer:**
Prevents a service from trying to execute an operation that is likely to fail. After a certain threshold of failures, the "circuit opens," and calls return immediately with an error (or fallback), giving the failing service time to recover.
