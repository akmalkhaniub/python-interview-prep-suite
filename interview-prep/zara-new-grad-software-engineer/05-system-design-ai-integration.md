# 05 - System Design & AI Integration

## Scalability Concepts

### 1. Vertical vs. Horizontal Scaling?
**Answer:**
- **Vertical:** Adding more RAM/CPU to an existing machine. Limit is physical hardware capacity.
- **Horizontal:** Adding more machines to the pool. Limit is practically infinite but introduces complexity (load balancing, state management).

### 2. What is a Load Balancer?
**Answer:**
A device (or software) that sits in front of your servers and distributes incoming traffic across them to ensure no single server is overwhelmed. Examples: AWS ELB, Nginx.

### 3. Caching Strategies?
**Answer:**
- **Client-side:** Browser cache.
- **CDN:** Edge caching for static assets.
- **Application side:** Using Redis or Memcached for frequently accessed DB queries or computed values.

## AI Integration for Software Engineers

### 4. How do you integrate an LLM into a Fullstack App?
**Answer:**
- **Backend:** Create an endpoint in Node.js that calls the OpenAI/Anthropic API.
- **Streaming:** Use Server-Sent Events (SSE) or WebSockets to stream the model's response back to the React frontend in real-time.
- **State:** Use a task queue (like BullMQ or RabbitMQ) if the AI processing takes a long time.

### 5. What is Prompt Engineering for Developers?
**Answer:**
Writing high-quality system prompts that guide the model to return structured data (e.g., JSON) instead of free-form text. 
- **Techniques:** Few-shot prompting (giving examples), Chain-of-thought (asking it to reason step-by-step), and output formatting instructions.

### 6. What are the challenges of building "AI-First" products?
**Answer:**
- **Non-determinism:** The model might give different answers to the same query.
- **Latency:** LLMs are slower than traditional APIs.
- **Cost:** Token usage can add up quickly.
- **Hallucination:** Verifying the correctness of the AI's output.
- **Security:** Preventing Prompt Injection.
