# 03. AWS Bedrock & Infrastructure

This guide focuses on the AWS-specific components of the AI agent platform, particularly **AWS Bedrock**.

## ☁️ AWS Bedrock Ecosystem
Bedrock is more than just an API; it's a full runtime for agents.

### 1. AgentCore Runtime
The JD mentions **AgentCore Runtime**. This refers to the managed infrastructure for running agentic loops.
- **Orchestration:** Managed by Bedrock, reducing the need for custom Python loops.
- **Action Groups:** How Bedrock Agents interact with Lambda functions or APIs (standardized tool calling).
- **Knowledge Bases:** Managed RAG (Retrieval-Augmented Generation) within Bedrock.

### 2. IAM & Security (The "Hard" Part)
A production role requires deep knowledge of IAM:
- **Trust Policies:** Ensuring `bedrock.amazonaws.com` can assume the execution role of your agent.
- **Permission Boundaries:** Restricting what the agent can do even if it has a powerful role.
- **Service-Linked Roles:** Automatically created by AWS to allow Bedrock to access other services like S3 or OpenSearch.

### 3. Compute & Deployment
- **ECR (Elastic Container Registry):** Storing Docker images for your FastAPI services and MCP servers.
- **Secrets Manager:** Storing API keys (OpenRouter, Pinecone, Perplexity) securely. Agents fetch these at runtime.
- **Networking (VPC):** Ensuring the agent can talk to the RDS (MySQL) database securely via VPC Endpoints or NAT Gateways.

## 🤖 Bedrock APIs to Know
- **ChatBedrockConverse:** The standard API for streaming multi-turn conversations.
- **ApplyGuardrail API:** Explicitly applying safety filters to user input or model output.
- **Prompt Caching:** (Specifically mentioned in "Nice to have") Reducing latency and cost for long system prompts.

## 🏗️ Architecture: The "Agentic Middleware"
In this role, you aren't just calling an LLM. You are building the layer *between* the LLM and the user.

```text
[User] -> [FastAPI] -> [Bedrock Guardrails] -> [LangGraph / AgentCore] -> [MCP Tool Servers] -> [RDS / S3]
```

## ❓ Common Interview Questions
1. **How do you troubleshoot a 403 Forbidden error in Bedrock?**
   - *Answer:* Check the IAM Role's trust policy, verify the identity has `bedrock:InvokeModel` permissions, and ensure the specific model (e.g., Claude 3.5 Sonnet) is enabled in the account's Model Access page.
2. **Explain Bedrock Prompt Caching.**
   - *Answer:* It allows you to cache the "system" or "context" part of a prompt. If multiple requests share the same prefix, you only pay for the full context once, and subsequent requests are faster and cheaper.
3. **What is a "Trust Policy" in the context of an AI Agent?**
   - *Answer:* It's a JSON document that defines which principals (like the Bedrock service) can assume the IAM role associated with the agent.

---
**Next Step:** Dive into data retrieval with [04-rag-vector-sql.md](04-rag-vector-sql.md).
