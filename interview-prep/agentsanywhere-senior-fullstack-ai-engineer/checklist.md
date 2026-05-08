# 7-Day Interview Prep Checklist: Senior Full-Stack AI Engineer (Agentsanywhere)

## Day 1: Next.js & TypeScript Mastery
- [ ] Review Next.js App Router, Server Components, and Server Actions.
- [ ] Practice setting up a tRPC router with Prisma and PostgreSQL.
- [ ] Build a simple SSE streaming endpoint to push mock agent updates to a UI.

## Day 2: Python & FastAPI AI Services
- [ ] Review FastAPI async patterns and dependency injection.
- [ ] Practice building a RAG pipeline (Chunking -> Embedding -> Vector Search).
- [ ] Study document parsing libraries for PDF and DOCX.

## Day 3: Agentic Loops & Tool Use
- [ ] Implement a basic "Tool-Use" loop (Model -> Parse Tool Call -> Execute -> Feed Back).
- [ ] Research vendor-specific tool-call quirks (Anthropic vs. OpenAI).
- [ ] Study context compaction strategies (How to keep relevant history only).

## Day 4: Cloud Sandboxes & Execution
- [ ] Experiment with the E2B SDK: Run code in a sandbox and monitor output.
- [ ] Review Docker container lifecycle and process supervision.
- [ ] Research "Snapshot and Restore" patterns for containerized environments.

## Day 5: Integrations & Real-time
- [ ] Review OAuth2 flows and webhook integration best practices.
- [ ] Study "Composio" or similar integration broker patterns.
- [ ] Practice handling background workers and fan-out updates to live UIs (WebSockets/SSE).

## Day 6: Reliability & Stochastic Debugging
- [ ] Research how to debug "infinite loops" and "drift" in LLM agents.
- [ ] Review LLM evaluation (Evals) techniques and tools (e.g., LangSmith, RAGAS).
- [ ] Study cost and latency optimization for multi-provider AI systems.

## Day 7: Ownership & Shipping Bias
- [ ] Prepare "Behavioral" stories: Times you shipped in ambiguity, debugged a production failure, or took full ownership of an AWS deployment.
- [ ] Practice a "System Design" interview: "Design a platform that can run 1000 concurrent AI developers."
- [ ] Final review of the Agentsanywhere mission and your "Strong opinions, light hold" technical views.
