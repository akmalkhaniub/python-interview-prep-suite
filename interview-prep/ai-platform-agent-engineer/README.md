# Interview Prep: AI Platform Agent Engineer (AWS Bedrock & MCP)

This module is designed for the **AI Engineer** role focused on building a production-grade AI agent platform. The role emphasizes **LangGraph**, **Model Context Protocol (MCP)**, and the **AWS Bedrock** ecosystem.

## 🚀 Role Overview
The core of this role is transitioning from simple LLM prototypes to robust, scalable agentic systems. You will be responsible for the "Middleware" and "Runtime" of AI agents, ensuring they are safe (Guardrails), accurate (Evals), and integrated (MCP).

## 🗺️ Study Roadmap

### 1. Agent Orchestration ([01-agent-architectures-langgraph.md](01-agent-architectures-langgraph.md))
- **LangGraph Topologies:** Master the Supervisor-Worker and Researcher-Analyst patterns.
- **State & Persistence:** Handling checkpointing, thread management, and complex state transitions.
- **Subagent Dispatch:** How to route tasks dynamically between specialized agents.

### 2. Model Context Protocol ([02-mcp-protocol-servers.md](02-mcp-protocol-servers.md))
- **MCP Fundamentals:** Understanding the open standard for tool and data integration.
- **Building Servers:** Using `FastMCP` and the official Python SDK to expose tools safely.
- **Versioning Tool Surfaces:** Managing evolving APIs for LLM consumption.

### 3. AWS Bedrock & Infrastructure ([03-aws-bedrock-infrastructure.md](03-aws-bedrock-infrastructure.md))
- **Bedrock AgentCore:** Deploying agents into the AWS runtime.
- **IAM & Security:** Trust policies for Bedrock roles, ECR for containerized tools, and Secrets Manager.
- **Networking:** RDS (MySQL) integration and VPC considerations for agents.

### 4. Advanced Retrieval & SQL ([04-rag-vector-sql.md](04-rag-vector-sql.md))
- **Pinecone Strategy:** Hybrid search, reranking, and chunking optimization.
- **Text-to-SQL:** Using Vanna AI to bridge natural language and relational data securely.

### 5. Production Evals & Guardrails ([05-evals-guardrails.md](05-evals-guardrails.md))
- **Observability:** Deep dive into **LangSmith** traces and debugging.
- **Guardrails:** Implementing tiered access using AWS Bedrock Guardrails and custom LLM classifiers.
- **LLM-as-Judge:** Creating evaluation harnesses to quantify improvement.

## 🛠️ Tech Stack Focus
- **Primary:** Python 3.10+, LangGraph, AWS Bedrock.
- **Secondary:** Pinecone, Vanna AI, FastAPI, MCP.
- **DevOps:** `uv`, `pytest`, ECR, IAM.

---

> [!TIP]
> Focus on the **"Supervisor-Researcher"** topology and **MCP servers**, as these are highlighted as specific 90-day success metrics in the JD.
