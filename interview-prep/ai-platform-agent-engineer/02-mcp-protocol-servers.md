# 02. Model Context Protocol (MCP) Servers

The **Model Context Protocol (MCP)** is an open standard that enables AI models to interact with local and remote data/tools seamlessly. This JD requires you to "Architect and ship MCP servers."

## 🧩 What is MCP?
MCP solves the problem of "bespoke tool integrations." Instead of writing a custom integration for every tool (Slack, GitHub, Local DB), you build an **MCP Server** that exposes tools in a standardized JSON-RPC format.

### Key Components:
- **MCP Host:** The LLM client (e.g., Claude Desktop, a LangGraph agent) that connects to the server.
- **MCP Server:** The service that exposes Resources, Prompts, and Tools.
- **Transports:** Usually StdIO (for local) or SSE (Server-Sent Events) for remote.

## 🛠️ Building an MCP Server
The JD mentions **FastMCP** (a high-level SDK) and the official SDKs.

### Example: FastMCP Server
```python
from mcp.server.fastmcp import FastMCP

# Create a server
mcp = FastMCP("Database-Tools")

@mcp.tool()
async def query_inventory(item_name: str) -> str:
    """Query the inventory database for a specific item."""
    # Logic to query RDS/MySQL
    return f"Found 5 {item_name} in stock."

if __name__ == "__main__":
    mcp.run()
```

### Advanced Features:
- **Resources:** Exposing read-only data (like a log file or a schema) as a URI.
- **Prompts:** Providing pre-defined templates for specific tasks.
- **Tool Versioning:** Since tools are basically APIs for LLMs, you must manage breaking changes (e.g., changing parameter names) carefully.

## 🚢 Shipping MCP in Production
The JD asks for "AWS-deployed runtime infrastructure."

1. **Dockerization:** MCP servers are often packaged as Docker containers.
2. **ECR:** Pushing the container to Amazon Elastic Container Registry.
3. **Deployment:** Running the container on ECS/Fargate or as a Lambda (using SSE transport).
4. **Middleware Stacks:** Adding auth and rate limiting to your MCP server to prevent prompt injection or resource exhaustion.

## ⚖️ MCP Tradeoffs
- **Pros:** Standardization, decoupled tools, reusable across different LLMs.
- **Cons:** Overhead of JSON-RPC, discovery latency, security risks (exposing local system access).

## ❓ Common Interview Questions
1. **How do you handle authentication in an MCP server?**
   - *Answer:* For SSE, use standard API keys/JWT. For StdIO, the host environment (like AWS) handles identity via IAM.
2. **Difference between a Resource and a Tool in MCP?**
   - *Answer:* **Resources** are for reading data (stateful). **Tools** are for performing actions (functional/side-effects).
3. **How do you test an MCP server?**
   - *Answer:* Use the `mcp-inspector` tool to manually verify tool signatures and responses, and `pytest` for unit testing the underlying logic.

---
**Next Step:** See how these servers are deployed on AWS in [03-aws-bedrock-infrastructure.md](03-aws-bedrock-infrastructure.md).
