# Web Performance & Scalability
**Focus:** Browser Performance, Horizontal Scaling, Reliability, Caching

---

## Section A: Application & Browser Performance

### Q1: Optimizing High-Traffic Web Apps
**Question:** How do you approach optimizing the end-to-end performance of a high-traffic web application, from the browser to the database?

**Expected Answer:**
Optimizing a web application requires a holistic approach across the entire stack. Starting at the **Database**, I ensure heavy queries are indexed, eliminate N+1 queries using ORM eager loading, and configure connection pooling to prevent connection exhaustion. At the **Backend Application** level, I move all blocking, synchronous operations (like sending emails or generating PDFs) to asynchronous background queues like Celery, ensuring the HTTP request returns to the user in milliseconds.

Moving to the **Edge/Network** layer, I heavily implement caching mechanisms like Redis for frequent database reads and configure a CDN (Content Delivery Network) like Cloudflare or GCP Cloud CDN. The CDN physically caches static assets (images, CSS, JS) at edge servers geographically close to the user, drastically reducing latency and offloading traffic from our origin servers. Finally, at the **Browser** level, I enforce techniques like minifying and compressing (GZIP/Brotli) assets, utilizing lazy-loading for images below the fold, and leveraging HTTP caching headers (`Cache-Control`) to prevent the browser from re-downloading unchanged files.

---

### Q2: Horizontal vs. Vertical Scaling
**Question:** What techniques do you use to scale a web application horizontally, and what architectural changes does it require?

**Expected Answer:**
Vertical scaling (scaling "up") means upgrading a single server with more CPU and RAM. It is easy but has hard hardware limits and provides no fault tolerance. Horizontal scaling (scaling "out") involves adding more servers to distribute the load, which provides infinite scaling and high availability.

To scale a Django application horizontally, the architecture must fundamentally change to become entirely **Stateless**. If server A handles the first user request and server B handles the second, server B must have all necessary context. This means we can no longer store user session data in local server memory or files; sessions must be centralized in a distributed cache like Redis. Similarly, user-uploaded files cannot be stored on the local disk; they must be uploaded directly to cloud object storage like GCP Cloud Storage or AWS S3. Once the application is perfectly stateless, we can deploy an Auto Scaling Group behind an HTTP Load Balancer, seamlessly spinning up new instances to handle traffic spikes.

---

## Section B: Reliability & Real-Time Communication

### Q3: Ensuring Distributed System Reliability
**Question:** How do you ensure the reliability and fault tolerance of a highly distributed software system?

**Expected Answer:**
Ensuring reliability in distributed systems requires anticipating failure at every level. At the infrastructure level, redundancy is non-negotiable. Compute instances must be spread across multiple Availability Zones, and databases must be configured for High Availability (HA) with automatic failover. 

At the application layer, I implement defensive programming patterns. Every network call to an external API or internal microservice must be wrapped in strict **Timeouts** to prevent thread starvation. I heavily utilize the **Circuit Breaker Pattern**; if a dependency goes down, the circuit opens, failing fast to protect our system rather than hanging. Furthermore, I implement exponential backoff and retry logic for transient network failures. Finally, robust observability is critical: without centralized logging, distributed tracing (like OpenTelemetry), and proactive alerting, you cannot maintain reliability because you are flying blind during an outage.

---

### Q4: The Role of Caching
**Question:** Explain the role of caching (using Redis or Memcached) in improving scalability. Where in the stack should caching be applied?

**Expected Answer:**
Caching is the most effective tool for protecting backend infrastructure from overwhelming traffic. By storing the results of expensive operations in ultra-fast, in-memory datastores like Redis, we serve subsequent requests in microseconds rather than re-computing the data. 

Caching should be applied strategically across multiple layers. **Database Caching** stores the results of complex, slow SQL queries. If a query takes 2 seconds but the data rarely changes, caching the result drastically reduces database load. **Template/Fragment Caching** in Django allows us to cache fully rendered blocks of HTML, preventing the server from repeatedly generating dynamic UI components. Finally, **Full-Page Caching** (often handled via middleware or a reverse proxy like Varnish) caches the entire HTTP response for anonymous users, completely bypassing the Django application and database for identical requests, which is critical for surviving massive traffic spikes.

---

### Q5: WebSockets vs. HTTP
**Question:** What are WebSockets, and how do they differ from traditional HTTP? When would you use them in a Django project?

**Expected Answer:**
Traditional HTTP is a strictly unidirectional, stateless, request-response protocol. The client must explicitly ask the server for data, and the server closes the connection once the response is sent. If an application needs live updates, HTTP forces the client to use "Long Polling"—repeatedly pinging the server to ask, "Is there new data yet?"—which generates massive, inefficient network overhead.

**WebSockets** provide a persistent, full-duplex, bidirectional communication channel over a single TCP connection. Once the connection is established (via an HTTP handshake upgrade), both the server and the client can push data to each other instantly at any time, with virtually zero latency and minimal overhead. In a Django project, I would implement WebSockets (using Django Channels and an ASGI server like Daphne) for features requiring true real-time interaction, such as live chat applications, collaborative document editing, or streaming live stock ticker dashboards.
