# Module 01: Scraping Architecture at Scale

This module focuses on the system architecture of production-grade web scraping pipelines, proxy management, and data ingestion strategies.

---

## Technical Q&A

### Q1: Design a distributed, fault-tolerant web scraping architecture capable of harvesting 10 million pages per day from multiple websites.
**Answer:**
Scaling a web scraping pipeline requires decoupling **job scheduling**, **network request execution**, and **data parsing** to prevent bottlenecks and isolate failures.

#### 1. System Architecture Layout:
```
 [Scheduler (Cron / Celery)] 
            │ (Pushes target URLs)
            ▼
   [Message Broker (Redis / RabbitMQ)]
            │ (Job Queues: partitioned by target domain)
            ▼
    [Worker Fleet (Docker / Kubernetes Pods)]
     ├── HTTP Requests Runner (Asynchronous / curl-impersonate / Playwright)
     └── Parser Engine (Extracts structured data)
            │ (Structured Payload)
            ▼
   [Ingestion Queue (Kafka)] ──► [Validator] ──► [Database (Postgres / Mongo)]
```

#### 2. Key Architectural Decisions:
*   **Job Scheduling & Queue Partitioning:**
    *   Separate message queues by target domain (e.g., `queue_google`, `queue_amazon`) to manage rate-limiting and proxy-routing rules independently per site.
    *   Maintain a Redis-based **deduplication cache** (using Bloom Filters) to avoid scraping duplicate URLs and wasting bandwidth.
*   **Worker Footprint:**
    *   Keep workers stateless. Run them as ephemeral Docker containers managed by Kubernetes. 
    *   For Javascript-heavy sites, run isolated instances of Headless Chrome via **Playwright** or **Puppeteer** within dedicated browser-less worker pods, recycling browser contexts after $50$ requests to prevent memory leaks.
*   **Data Validation Loop:**
    *   Before writing raw extraction data to the database, route it through an ingestion worker that verifies the schema (using Pydantic models). If a site updates its HTML structure, the parser might return empty values. If the validation check fails (e.g., `extracted_price == null`), quarantine the payload and trigger an alert to prevent corrupted data from polluting production tables.

---

### Q2: Compare Datacenter, Residential, and Mobile proxies in the context of scraping highly-protected sites. How does a backconnect proxy gateway operate?
**Answer:**

| Proxy Type | IP Source | Success Rate | Cost | Detection Risk | Typical Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Datacenter** | Virtual servers in hosting centers (AWS, DigitalOcean). | Low (Highly blocked). | Very Low. | High (Easy to identify subnet ranges). | Simple APIs, unprotected static pages. |
| **Residential** | Real home internet connections (ISPs / Wi-Fi). | High. | Medium (Billed by GB). | Low (Look like organic domestic users). | E-commerce sites, price monitoring. |
| **Mobile** | Cell carriers (3G/4G/5G towers). | Very High. | High (Billed by GB). | Extremely Low (IPs are shared by thousands of users via CGNAT). | Highly protected sites (Google, social media). |

**Backconnect Proxy Gateway Operation:**
- In a traditional proxy setup, the scraper client manually changes the proxy IP address in the request header.
- A **Backconnect Gateway** provides a single endpoint domain and port (e.g., `gate.proxyprovider.com:8000`).
- When the scraper client sends an HTTP request to the gateway, the gateway's internal router dynamically assigns the request to a random active IP from their massive residential or mobile pool.
- **Session Management:** The gateway allows session pinning using custom headers (e.g., `Proxy-Authorization: user-session-12345`). This instructs the gateway to route subsequent requests from that client thread to the exact same IP node, allowing the scraper to maintain session cookies, logins, or multi-step checkout paths without triggering security flags due to IP hopping.

---

### Q3: Describe the optimal concurrency models and throttling strategies for high-throughput Python-based scraper scripts.
**Answer:**
Scraping is heavily network-bound. Traditional multi-threading or multi-processing models are resource-heavy, spending CPU time waiting for network sockets to open.

1. **Concurrency Model (Asyncio + HTTPX / Aiohttp):**
   - Use asynchronous concurrency (`asyncio`). A single process run loop can handle thousands of concurrent requests using asynchronous sockets, yielding CPU execution to other tasks while waiting for response bytes.
   - For high-performance HTML parsing, offload heavy parsing operations to CPU-bound worker pools (`ProcessPoolExecutor`) to prevent blocking the main event loop.

2. **Throttling & Concurrency Controls:**
   - **Token Bucket Algorithm:** Implement an asynchronous rate limiter to control requests per second (RPS) to a specific target domain:
     ```python
     import asyncio
     
     class AsyncRateLimiter:
         def __init__(self, rps: float):
             self.delay = 1.0 / rps
             self.lock = asyncio.Lock()
             
         async def wait(self):
             async with self.lock:
                 await asyncio.sleep(self.delay)
     ```
   - **Dynamic Backoff:** Do not hardcode sleep times. Adjust scraping speed dynamically based on HTTP response status codes:
     - **HTTP 429 (Too Many Requests):** Read the `Retry-After` header if present. Otherwise, multiply the request delay by a factor of 2.
     - **HTTP 403 / 503:** Rotate the proxy IP and backoff exponentially.

---

### Q4: Compare HTML parser performance libraries in Python. When would you use BeautifulSoup, lxml, and selectolax?
**Answer:**

1. **BeautifulSoup:**
   - **Performance:** Slow (pure Python overhead).
   - **Parser Backend:** Typically uses `html5lib` or `lxml`.
   - **Use Case:** Small-scale prototyping or parsing poorly formatted, corrupted HTML documents (high fault tolerance).

2. **lxml:**
   - **Performance:** Fast (written in C wrapping the `libxml2` library).
   - **Query Selector:** Supports XPath 1.0 and CSS Selectors.
   - **Use Case:** Enterprise scrapers extracting deep, hierarchical nodes using complex XPath queries.

3. **selectolax (Modest Engine):**
   - **Performance:** Extremely Fast (approx. **10x to 30x faster** than BeautifulSoup/lxml, written in C wrapping the `Lexbor` HTML parser).
   - **Query Selector:** Strictly CSS Selectors.
   - **Use Case:** High-throughput pipelines processing millions of documents, where HTML parsing CPU cycles directly limit the system ingestion rate.
