# Interview Questions & Expert Answers: Senior Python AI/ML Engineer

This document contains tailored interview questions and high-level answers designed for the Senior Python AI/ML Engineer role at Hybrid Mediaworks. The focus spans FastAPI/Django backends, LangGraph/RAG workflows, Web Scraping (Playwright), and MLOps/Computer Vision.

---

## Part 1: Agentic Workflows & RAG Architectures

### 1. How would you design a production-grade RAG pipeline that handles both unstructured PDF data and real-time scraped web content?
**Expert Answer:** 
"A robust RAG system requires a modular data ingestion pipeline. 
1. **Ingestion & Parsing:** For PDFs, I would use tools like Unstructured.io to extract text and tables. For web data, I'd use Playwright (for dynamic content) and Scrapy, orchestrated via Celery or Airflow.
2. **Chunking & Embedding:** Text is chunked semantically (e.g., recursive character splitting) to maintain context. I'd use an optimized embedding model (like OpenAI's `text-embedding-3` or a self-hosted BGE model) and upsert to a vector database like Pinecone or Chroma.
3. **Retrieval Strategy:** To ensure high relevance, I implement Hybrid Search (combining Dense Vector Search with Sparse BM25 keyword search) and a Re-ranking step (using Cohere or Cross-Encoder) to order the top results before passing them to the LLM context window."

### 2. Can you explain how you use LangGraph for autonomous workflows compared to standard LangChain chains?
**Expert Answer:** 
"Standard LangChain chains or traditional DAGs are great for predictable, linear tasks. However, autonomous agents often require cyclical, stateful logic—for example, an agent scraping a website, failing to find a specific element, and needing to 'think' and try a different URL.
LangGraph treats the workflow as a state machine. I define nodes as specific tasks (e.g., 'Extract Data', 'Validate with LLM', 'Use Search Tool') and edges as conditional logic. The global state object is passed between these nodes. This allows for complex 'while loops' in the agent's logic, enabling robust error handling, human-in-the-loop pauses, and complex multi-agent orchestration that standard sequential chains cannot handle."

---

## Part 2: Backend Development & Scalability

### 3. How do you optimize a FastAPI application that serves deep learning inference and handles thousands of concurrent requests?
**Expert Answer:** 
"FastAPI is exceptionally fast due to Starlette and async support, but ML inference is CPU/GPU blocking. 
1. **Async & Non-Blocking:** I ensure all I/O bound operations (database calls, external API calls) use `async/await`.
2. **Offloading ML:** I never run heavy ML inference (like YOLO or PyTorch models) directly in the FastAPI event loop. I offload inference to a dedicated task queue (Celery + Redis/RabbitMQ) or a specialized inference server like Triton Inference Server or Ray Serve. FastAPI simply acts as the asynchronous gateway.
3. **Caching & Concurrency:** I implement Redis for caching frequent identical requests. I deploy the FastAPI app using Uvicorn with multiple Gunicorn worker processes configured to maximize CPU core utilization."

### 4. When would you choose Django over FastAPI for an AI project?
**Expert Answer:** 
"FastAPI is my go-to for high-performance microservices, async processing, and lightweight model serving. However, I choose Django when the project requires a heavy, monolithic web application with complex relational data models, user authentication, a built-in admin panel, and rapid MVP development. If an AI platform needs extensive user management, subscription billing, and complex relational CRUD operations alongside the AI features, Django’s built-in ORM and Admin interface save weeks of development time."

---

## Part 3: Data Orchestration, CV & MLOps

### 5. You need to scrape thousands of dynamic web pages daily to feed an LLM. How do you scale Playwright/Scrapy while avoiding IP bans?
**Expert Answer:** 
"Scaling web scraping requires a distributed architecture.
1. **Framework:** I use Scrapy for speed and scalability. For JavaScript-heavy or single-page applications, I integrate Playwright with Scrapy (e.g., `scrapy-playwright`).
2. **Concurrency:** I distribute the crawling tasks across multiple worker nodes using a message broker (RabbitMQ/Redis) and Scrapy Cluster.
3. **Avoiding Bans:** I implement a rotating residential proxy pool (like BrightData or Oxylabs). Furthermore, I randomize User-Agents, implement intelligent delays (auto-throttling), and manage cookies carefully. Playwright's stealth plugins are also utilized to bypass advanced bot detection (like Cloudflare)."

### 6. How do you handle CI/CD and Observability for an LLM application deployed on Kubernetes?
**Expert Answer:** 
"Deploying LLMs requires standard DevOps plus MLOps.
- **CI/CD:** I use GitHub Actions/GitLab CI. Code changes trigger unit tests and integration tests. Docker images are built and pushed to a registry (ECR/GCR). Helm charts update the Kubernetes deployments.
- **AI Observability:** Standard APM (like Datadog/Prometheus) tracks API latency and pod health. For the AI layer, I integrate tools like LangSmith or Phoenix/Arize to monitor LLM specific metrics: token usage, prompt latency, hallucination rates, and user feedback (thumbs up/down). This telemetry is crucial for knowing when to fine-tune the model or update the RAG prompt."
