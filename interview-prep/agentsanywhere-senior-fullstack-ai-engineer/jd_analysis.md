# Job Description Analysis: Senior Full-Stack AI Engineer - Agentsanywhere

## Role Overview
**Company:** Agentsanywhere
**Position:** Senior Full-Stack AI Engineer
**Mission:** Building a multi-agent platform where LLMs plan, investigate, execute, and ship real software end-to-end.
**Philosophy:** The model is part of the runtime, not just a bolted-on feature.
**Experience:** 5+ years shipping production systems at scale.

## Core Competencies

### 1. Full-Stack Engineering (TS + Python)
- **Frontend/Backend (TS):** Next.js (App Router), Prisma, PostgreSQL, NextAuth, Tailwind, tRPC.
- **Backend/AI (Python):** FastAPI (async), vector DBs, RAG pipelines, document parsing (PDF/DOCX).
- **Real-time:** SSE (Server-Sent Events), WebSockets, background workers.

### 2. Agentic AI Systems
- **Loops:** Multi-step tool-use loops.
- **Providers:** Anthropic, OpenAI, Google.
- **Engineering:** Prompt + context engineering, memory + compaction.
- **Quirks:** Handling tool-call quirks across different vendors.

### 3. Infrastructure & Sandboxing
- **Sandboxes:** E2B, Docker, container lifecycle (file/process supervision).
- **Cloud:** AWS (Deployment, scaling, monitoring).
- **Snapshotting:** snapshot/restore of container states.

### 4. Integrations & Business Logic
- **Brokers:** Composio-style brokers for live integrations.
- **Services:** GitHub, Slack, Stripe, OAuth, Webhooks.

### 5. Systems Engineering & Reliability
- **Schema Evolution:** Designing DB schemas that can evolve weekly without breaking production (Prisma migrations, feature flags).
- **Stochastic Debugging:** Identifying and fixing non-deterministic failures: drift, hallucinations, infinite loops, and partial execution failures.
- **Scale:** Building multi-tenant systems for real-time users with high reliability and low latency.

## Key Requirements
- **Execution-Driven:** Building systems where model output drives execution, not just chat.
- **Robustness:** Debugging stochastic systems, drift, hallucinations, and infinite loops.
- **Ownership:** End-to-end ownership on AWS without hand-holding.
- **Mindset:** Strong bias to ship, comfortable in ambiguity. "Strong opinions, light hold."

## Interview Themes to Prepare
1.  **Agentic Runtimes:** Designing a robust execution loop where an LLM can use tools to modify a codebase or interact with an API.
2.  **Stochastic Debugging:** How to test and debug non-deterministic agent behavior (e.g., stopping infinite loops, handling partial failures).
3.  **High-Velocity Schema Evolution:** Strategies for managing weekly schema changes in a multi-tenant PostgreSQL/Prisma environment.
4.  **Memory & Compaction:** Strategies for long-term agent memory and context window management (summarization, sliding windows).
5.  **Sandbox Security:** Securing E2B/Docker sandboxes where untrusted LLM-generated code might be executed.
6.  **SSE & Real-time UX:** Managing complex SSE streams for real-time agent "thinking" and "action" updates in a Next.js UI.
7.  **Multi-Provider Strategy:** Abstracting tool-use across Anthropic (Claude) and OpenAI (GPT-4) while handling vendor-specific quirks.
8.  **Data Modeling for Agents:** Designing schemas that track agent states, tool outputs, and historical execution traces.
9.  **Cost & Latency Optimization:** Balancing the use of powerful (expensive/slow) models for planning vs. faster models for execution.
10. **Integration Architecture:** Building a scalable system for managing hundreds of third-party integrations (Composio, webhooks).
11. **Multi-tenant Reliability:** Ensuring isolation and performance for concurrent agent runs across different customers.
