# Module 05: Platform Reliability, Latency & Economics

Scaling AI inside India's largest banks and NBFCs requires institutional-grade reliability, strict regulatory compliance, and control over operational unit economics. An Engineering Manager at Rezolv must solve these engineering problems while keeping system latency low.

---

## 1. Shaving Latency to Sub-800ms
For a voice agent, conversational delay > 1 second causes confusion and constant interruptions. We target an end-to-end latency budget of **< 800ms**:

```
+-------------------------------------------------------------------------------+
|                        Latency Budget (Total: 800ms)                          |
+------------+---------------+-------------------------+------------+-----------+
| VAD End    | ASR Stream    | LLM Time-to-First-Token | TTS Stream | Telephony |
| (150ms)    | (150ms)       | (250ms)                 | (150ms)    | (100ms)   |
+------------+---------------+-------------------------+------------+-----------+
```

### Techniques to Shave Off Milliseconds
1.  **VAD Optimization:** Run VAD locally on the orchestrator server in C++ or Go, avoiding roundtrips.
2.  **ASR Chunk Streaming:** Stream raw audio to ASR via persistent WebSockets. The ASR engine returns "interim transcriptions" continuously.
3.  **LLM TTFT (Time to First Token):** Use models hosted on high-performance inference hardware (Groq, TensorRT-LLM, Gemini Flash) and stream response tokens.
4.  **TTS Streaming:** Send text to the TTS engine as soon as the first complete clause (e.g., 3-5 words) is generated, using TTS models designed for streaming (ElevenLabs Turbo or Cartesia).
5.  **Co-Location:** Host the orchestrator backend in the same cloud region as the ASR and TTS endpoints (typically India Central or South for local compliance).

---

## 2. Multi-Tenant Banking SaaS & Compliance
Banks handle sensitive customer financial information. Building multi-tenant architectures requires strict data segregation:

*   **Logical Data Isolation:** Every database row has a `tenant_id` index. All backend SQL/NoSQL queries must enforce a `tenant_id` filter (using middleware or database-level policies).
*   **Physical Data Isolation:** For high-tier banks (like ICICI Bank), data must be hosted on separate databases or physically isolated server instances.
*   **Data Residency & GDPR/RBI Guidelines:** The RBI requires all banking data (identity, transaction histories, calls) to reside within India.
*   **Encryption:** Enforce TLS 1.3 for data in transit and AES-256 for data at rest. Implement Customer Managed Keys (CMK) for key rotation.

---

## 3. Unit Economics of AI at Scale
Running GenAI agents is expensive. We maintain profitability by optimizing the cost per call minute:

1.  **Semantic Caching:** If a user asks a common question (e.g., "What are your customer care hours?"), we query a vector database (e.g., Qdrant or pgvector) for semantically similar cached answers. If a match is found (similarity > 0.95), we return the cached response, saving LLM tokens.
2.  **Model Routing:** Route simple turns (e.g., "Yes", "No", "Thank you") to cheap, fast models (GPT-4o mini, Llama-3-8B). Route complex negotiations (e.g., loan restructuring offers) to larger models (Claude Sonnet).
3.  **Dynamic Rate Limiting:** Enforce strict token and request limits per customer session to prevent malicious DDoS or rogue agent loops from exhausting budgets.

---

## 4. Target Interview Questions & High-Score Answers

### Q1: How do you optimize the system to achieve an end-to-end voice latency of under 800ms? What are the main bottlenecks?
**Answer:**
We split the latency budget into five distinct stages:
1.  **VAD (150ms):** We run Silero VAD locally. We set the silence threshold to 400ms. If the user pauses, we begin background LLM processing on the interim transcript, pre-fetching options.
2.  **ASR (150ms):** We stream raw audio chunks via WebSocket to Deepgram/Sarvam. We read the transcript results instantly from the streaming buffer.
3.  **LLM TTFT (200-250ms):** We use fast models (like `Gemini-1.5-Flash` or self-hosted Llama-3-8B-Instruct on vLLM/TRT-LLM). We start streaming output tokens immediately.
4.  **TTS (150ms):** We stream text to TTS using ElevenLabs/Cartesia. We do not wait for the entire sentence. We send text chunks containing complete phrases (delineated by punctuation) to generate audio.
5.  **Telephony RTP (100ms):** We feed the generated audio directly back to the Twilio/SIP audio channel.
*Bottlenecks:* The biggest bottleneck is usually LLM Time to First Token and VAD end-pointing. We mitigate this by using high-throughput hosting (vLLM) and tuning VAD parameters dynamically based on call conditions.

### Q2: Indian banks often require on-premise deployments or hosting inside their private VPC (e.g., AWS Outposts or local Azure/GCP data centers). How do you design your deployment pipeline to support this?
**Answer:**
We build a containerized, cloud-agnostic architecture:
1.  **Kubernetes & Helm:** We package the entire Agentic OS (telephony bridge, core orchestrator, analytics, databases) using Docker and Helm charts.
2.  **Local LLM Hosting:** To support on-prem, we deploy models (like Llama-3 or Mistral) using vLLM inside the VPC, running on local NVIDIA A10G/H100 GPUs.
3.  **Local ASR/TTS:** We use containerized versions of Deepgram (Deepgram On-Premise) and local TTS engines (e.g., Coqui or XTTS).
4.  **Database Agnosticism:** We write database layers using Hibernate or SQLAlchemy, allowing us to swap between Managed RDS, local PostgreSQL, or specialized enterprise databases like Oracle depending on the bank's local stack.

### Q3: How do you design a database layer that enforces strict multi-tenancy and data isolation across multiple lenders?
**Answer:**
We implement a hybrid isolation strategy:
1.  **Logical Segregation (SaaS):** For mid-sized NBFCs, we use a shared database cluster. Every query is filtered via global database middleware (e.g., PostgreSQL Row-Level Security - RLS). Any query without a valid tenant header is rejected at the connection pool layer.
2.  **Physical Segregation (Enterprise):** For Tier-1 banks, we spin up dedicated Postgres databases and separate Kubernetes namespaces using automated Terraform modules.
3.  **Data Masking:** Sensitive data (PAN, Aadhaar, account numbers) are hashed or masked at the ingestion boundary. Only authorized service components can decrypt this data.

### Q4: If your LLM token costs increase by 40% during a collections campaign, how do you diagnose the issue and optimize costs without degrading call quality?
**Answer:**
We diagnose and optimize using a structured audit:
1.  **Trace Analysis:** We check our observability traces (Langfuse/LangSmith) to identify if the system is repeating prompts or if conversations are running longer than average.
2.  **Context Window Bloat:** We check if we are sending unnecessary context (e.g., too many past call histories or redundant RAG documents). We trim system prompts and keep historical logs brief.
3.  **Semantic Cache Implementation:** We check the hit-rate of our Redis semantic cache. If it is low, we adjust the similarity threshold or populate the cache with answers to common questions.
4.  **Inference Routing:** We route transactional queries (e.g., "Confirm payment date") to cheaper models (Llama-3-8B), reserving premium models (Claude Sonnet) only for complex conversations.
5.  **Infinite Loop Detection:** We inspect if any agent got caught in an loop of self-correction or tool calls, and optimize the state machine constraints.
