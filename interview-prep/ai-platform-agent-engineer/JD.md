# AI Engineer (Production Agent Platform)

## About the role
We're hiring an AI Engineer to own significant pieces of our production AI agent platform. You'll architect, build, and operate Python services that run LLM agents at scale — vector search, MCP tool servers, tiered guardrails, and AWS-deployed runtime infrastructure. This is a backend, hands-on role with real ownership: you'll make architecture calls, set patterns the rest of the team follows, ship to production every week, and work directly with clients to understand what to build.

We work fully remote.

---

## What you'll do
- **Own end-to-end design and delivery of LangChain / LangGraph agent systems** — supervisor-researcher topologies, deep-research pipelines, middleware stacks, subagent dispatch, memory, and summarization.
- **Architect and ship MCP (Model Context Protocol) servers** — design tool surfaces, version them, and integrate them into agents and external clients.
- **Drive vector search strategy on Pinecone** — chunking, embedding choices, multi-query and rerank pipelines, evaluation of retrieval quality.
- **Build and harden text-to-SQL systems (Vanna AI / similar)** over real production databases — including schema training, query validation, and safety controls.
- **Deploy and operate agents on AWS Bedrock** — ChatBedrockConverse, AgentCore Runtime, Bedrock Guardrails, ApplyGuardrail API, prompt caching. Own the AWS surface: IAM, ECR, Secrets Manager, RDS, networking.
- **Design guardrail and tier-based access layers** — pre/post-processing, regex filters, LLM classifiers, Bedrock Guardrails — across multiple user populations.
- **Lead evaluation and observability** — LangSmith trace extraction, eval harnesses, LLM-as-judge calibration.
- **Build production FastAPI services** with streaming, auth, and structured logging.
- **Work directly with clients and stakeholders** — translate vague business asks into concrete technical specs.
- **Mentor more junior engineers** and raise the bar on engineering practices.

---

## Required Skills
- **4+ years Professional Python** (async, type hints, uv/poetry).
- **2+ years building production LLM applications** (not prototypes).
- **Strong hands-on experience with LangChain / LangGraph**.
- **Hands-on AWS** (IAM, ECR, Secrets Manager, RDS).
- **Production experience with AWS Bedrock** (specifically preferred).
- **Solid grasp of Vector DBs** (Pinecone, etc.) and retrieval quality.
- **Working knowledge of MCP** (FastMCP or official SDKs).
- **Deep familiarity with LLM API mechanics** (tool calling, prompt caching, context management).
- **SQL competence** (queries, indexes, query plans).
- **Tracing / Observability** (LangSmith, Langfuse, OpenTelemetry).

---

## Nice to Have
- Anthropic Claude API depth (prompt caching, extended thinking).
- AWS Bedrock AgentCore Runtime experience.
- Evaluation frameworks (LLM-as-judge, GEPA/DSPy).
- Docker/ECR workflows.
- FastAPI at scale.
- Streamlit for internal tooling.

---

## Tech Stack
- **Languages:** Python 3.10+, SQL
- **Frameworks:** LangChain, LangGraph, DeepAgents, MCP
- **Models:** Anthropic Claude (via AWS Bedrock)
- **Search:** Pinecone, Perplexity API
- **Data:** MySQL (RDS), Vanna AI (text-to-SQL)
- **Cloud:** AWS (Bedrock, AgentCore, ECR, IAM, Secrets Manager, RDS, S3)
- **Observability:** LangSmith
- **Tooling:** uv, FastAPI, pytest, ruff, black, Streamlit
