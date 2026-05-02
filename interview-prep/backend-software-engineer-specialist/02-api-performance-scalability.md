# 02 - API Performance & Scalability

## Performance Optimization

### 1. How do you identify a "Performance Bottleneck" in an API?
**Answer:**
- **Profiling:** Using tools like `cProfile` (Python) or `VisualVM` (Java).
- **Monitoring:** Looking at p95/p99 latency metrics.
- **Tracing:** Identifying slow database queries or slow external API calls.
- **Load Testing:** Stressing the system to see where it breaks (JMeter/Locust).

### 2. Difference between "Latency" and "Throughput"?
**Answer:**
- **Latency:** The time taken to process a single request (e.g., 50ms).
- **Throughput:** The number of requests processed per second (e.g., 1000 RPS).

### 3. How do you implement "API Caching"?
**Answer:**
- **In-memory:** Caching small, static data inside the application process.
- **Distributed:** Using **Redis** or **Memcached** for data shared across multiple instances.
- **HTTP Caching:** Using `ETag` or `Cache-Control` headers to allow browsers/CDNs to cache responses.

## Scalability Strategies

### 4. Horizontal vs. Vertical Scaling?
**Answer:**
- **Vertical:** Adding more CPU/RAM to a single server (easier, but limited).
- **Horizontal:** Adding more server instances (requires a load balancer and stateless design).

### 5. What is "Database Sharding"?
**Answer:**
Splitting a large database into smaller, faster pieces (shards) based on a key (e.g., `user_id`). Each shard lives on a different server.

### 6. Role of "Asynchronous Processing" in scalability?
**Answer:**
Using Message Queues (Kafka/RabbitMQ) to offload time-consuming tasks (e.g., image processing, email sending) from the main request/response cycle. This keeps the API responsive and handles traffic spikes better.
