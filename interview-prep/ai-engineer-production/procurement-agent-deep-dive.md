# 🎯 AI Engineer Interview Deep-Dive: Procurement Intelligence Agent

> **Project:** [procurement-intelligence-agent](https://github.com/akmalkhaniub/procurement-intelligence-agent)
> **Stack:** LangGraph · MCP · Pinecone · Vanna AI · AWS Bedrock · Guardrails · LangSmith · FastAPI

---

## 1. LangGraph Supervisor-Researcher Topology

### 1.1 State Management

**Q: How do you handle long-running agent states and memory across sessions?**

**Answer:**

Our `AgentState` is a `TypedDict` with an append-only message history using LangGraph's `add_messages` reducer:

```python
class AgentState(TypedDict):
    messages: Annotated[list[BaseMessage], add_messages]  # append-only
    original_query: str
    context_docs: list[dict]
    sql_results: list[dict]
    iterations: int
    max_iterations: int
```

**Key design decisions:**

1. **Append-only messages** — Every agent node appends its output as an `AIMessage`, creating a full audit trail. The supervisor reads the last 10 messages for context window management.

2. **Session persistence** — For multi-turn conversations, we use LangGraph's `SqliteSaver` or `PostgresSaver` checkpointer:
```python
from langgraph.checkpoint.postgres import PostgresSaver
graph = build_graph().compile(checkpointer=PostgresSaver(conn_string))
```

3. **State partitioning** — We separate ephemeral state (iterations, next_agent) from persistent state (messages, session_id). Ephemeral state resets per query; persistent state survives across turns.

**Advanced improvement I'd make:**

- **Sliding window + summarization**: Instead of keeping all messages, I'd implement a summarization agent that compresses older messages into a summary node when the context exceeds 8K tokens. This prevents context window overflow on long procurement analysis sessions.
- **Redis-backed state for horizontal scaling**: In production with multiple ECS tasks, use Redis as the checkpoint backend instead of PostgreSQL to reduce serialization latency from ~15ms to ~3ms.

---

### 1.2 Error Handling & Self-Correction Loops

**Q: How do you prevent agents from entering infinite loops?**

**Answer:**

Three layers of protection:

**Layer 1 — Hard iteration guard:**
```python
if state["iterations"] >= state["max_iterations"]:  # default=5
    return {"next_agent": "writer"}  # force termination
```

**Layer 2 — Guardrail short-circuit:**
If the SQL analyst detects a forbidden pattern (DROP, DELETE), it sets `guardrail_triggered=True`, and the conditional edge skips back to the writer immediately — no re-routing through the supervisor.

**Layer 3 — Self-correction via structured output validation:**
The supervisor returns JSON with `{"next_agent": "...", "reasoning": "..."}`. If JSON parsing fails, we default to `"writer"` with a fallback response rather than re-prompting (which risks loops).

**Advanced pattern I'd add:**

- **Exponential backoff on repeated routing**: If the supervisor routes to `researcher` 3 times consecutively without new documents, force it to `writer` with a "low confidence" flag. This prevents the "no results → search again → no results" loop.
- **LangGraph `interrupt_before`**: Use human-in-the-loop breakpoints for high-stakes queries (e.g., queries touching contracts > $1M):
```python
graph = builder.compile(interrupt_before=["sql_analyst"])
```

---

### 1.3 Subagent Dispatch

**Q: How do you decide when to hand off vs. keep in the orchestrator?**

**Answer:**

The supervisor uses a structured routing prompt that classifies queries into three intent categories:

| Intent Signal | Route To | Example |
|---|---|---|
| Needs unstructured knowledge | `researcher` | "What's our vendor onboarding policy?" |
| Needs structured data | `sql_analyst` | "Total spend by vendor this FY?" |
| Both gathered, needs synthesis | `writer` | After researcher + sql_analyst return |
| Answer ready | `FINISH` | Draft validated |

**Design principle: "Route, don't reason."** The supervisor never answers directly — it only routes. This separation ensures each agent has a single responsibility and can be evaluated independently.

**Advanced patterns:**

- **Parallel dispatch**: Currently sequential. I'd upgrade to LangGraph's `Send()` API for parallel fan-out when both researcher AND sql_analyst are needed:
```python
def route_supervisor(state):
    if needs_both(state):
        return [Send("researcher", state), Send("sql_analyst", state)]
```
- **Confidence-based re-routing**: If the writer's confidence score < 0.6, automatically route back to the supervisor for a second research pass rather than returning a low-quality answer.

---

## 2. Model Context Protocol (MCP)

### 2.1 Tool Design & Versioning

**Q: How do you version tool schemas?**

**Answer:**

Our FastMCP server exposes 3 tools with rich docstrings that serve as the tool's "contract":

```python
@mcp.tool()
async def query_database(
    question: str,
    tier: str = "PUBLIC",     # Controls row limits
    dry_run: bool = False,    # Returns SQL without executing
) -> dict:
    """Execute natural language queries against procurement MySQL..."""
```

**Versioning strategy:**
- The MCP server itself is versioned (`version="1.0.0"` in FastMCP config)
- Tool signatures are backward-compatible: new parameters always have defaults
- Breaking changes get a new tool name (`query_database_v2`) rather than modifying the existing one
- We use `Dockerfile.mcp` for independent deployment — tool server can be updated without redeploying the API

**Advanced decisions:**

- **Schema registry**: In production, I'd publish tool schemas to a central registry (S3 or DynamoDB) that agents query at startup. This decouples tool discovery from deployment.
- **Capability negotiation**: MCP supports capability exchange during `initialize()`. I'd use this to expose different tool sets based on the caller's tier.

### 2.2 MCP vs. REST Tool-Calling

**Q: Why MCP over standard REST?**

| Dimension | MCP | REST Tool-Calling |
|---|---|---|
| **Protocol** | Bidirectional (server can push updates) | Request-response only |
| **Schema discovery** | Built-in `tools/list` | Manual OpenAPI spec |
| **Streaming** | Native via SSE / streamable-http | Requires custom implementation |
| **Agent integration** | `langchain-mcp-adapters` auto-converts to LangChain tools | Manual function wrapping |
| **Latency** | ~5ms overhead (protocol framing) | ~2ms overhead |
| **Multi-model** | Any LLM can use the same tools | Tied to specific function-calling format |

**My honest take:** MCP adds ~3ms latency per tool call, which is negligible versus the 2-5s LLM inference time. The win is **portability** — the same MCP server works with Claude, GPT-4, Gemini, or any LLM that supports tool use. In a multi-model production environment, this is a significant operational advantage.

**When I'd skip MCP:** For latency-critical inner loops (e.g., a tool called 50 times per query), I'd use direct function calls and reserve MCP for the outer tool surface exposed to agents.

---

## 3. Text-to-SQL & Safety

### 3.1 Query Validation

**Answer:**

Three-layer defense:

```
Layer 1: Pattern matching (11 forbidden SQL keywords)
    → DROP, DELETE, TRUNCATE, ALTER, CREATE, INSERT, UPDATE, GRANT, REVOKE, EXEC, EXECUTE

Layer 2: Table whitelist validation
    → Only vendors, purchase_orders, invoices, contracts allowed
    → INFORMATION_SCHEMA explicitly blocked

Layer 3: Tier-based row limit injection
    → INTERNAL: 500, PARTNER: 100, PUBLIC: 25
    → Existing LIMIT clauses are REPLACED (not appended) to prevent bypass
```

**Critical subtlety:** We replace existing LIMIT clauses rather than just appending. If Vanna generates `LIMIT 10000`, our `_inject_limit()` rewrites it to `LIMIT 25` for PUBLIC tier. This prevents data exfiltration through crafted questions.

### 3.2 Advanced SQL Optimizations I'd Add

**N+1 Detection:**
```python
def detect_n_plus_1(sql: str) -> bool:
    """Flag queries that would trigger N+1 in application layer."""
    # If query selects IDs without JOINing related data,
    # warn that the caller might loop over results
    has_id_select = re.search(r"SELECT.*_id\b", sql, re.IGNORECASE)
    lacks_join = "JOIN" not in sql.upper()
    return bool(has_id_select and lacks_join)
```

**Missing index detection:**
```python
def suggest_indexes(sql: str, schema: dict) -> list[str]:
    """Analyse WHERE/JOIN clauses against known indexes."""
    # Parse WHERE conditions, check if filtered columns have indexes
    # Return suggestions like "CREATE INDEX idx_vendor_risk ON vendors(risk_score)"
```

### 3.3 Confidence Scoring Before Execution

**Current approach:** Vanna AI generates SQL, we validate it, then execute. But we don't score confidence.

**What I'd add:**
```python
async def confidence_gate(question: str, generated_sql: str) -> float:
    """Use LLM to score SQL confidence before execution."""
    prompt = f"Rate 0-1 how likely this SQL answers the question correctly.\nQ: {question}\nSQL: {generated_sql}"
    score = await llm.ainvoke(prompt)
    if score < 0.7:
        # Try regeneration with more context
        return await vanna.generate_sql(question, hint="Include JOINs for vendor names")
    return score
```

---

## 4. RAG & Vector Strategy

### 4.1 Eval-Driven Retrieval Quality

**Q: How did you achieve hallucination reduction?**

**Answer:**

Our eval-driven feedback loop:

```
Production traces (LangSmith) → Sample 100/week → LLM-as-Judge scores
→ Identify low-faithfulness answers → Root-cause: retrieval gaps vs. synthesis errors
→ Fix: adjust chunking, add golden examples, tune reranker threshold
```

**Concrete improvements that drove quality:**

1. **Multi-query expansion** (3 variants per query) — Increased recall by ~25%
2. **Cohere reranker v3** — Reduced false positives in top-5 by ~30%
3. **Tier-based metadata filtering** — Eliminated information leakage across access tiers
4. **Retrieval score threshold** (< 0.65 → trigger web search fallback) — Catches knowledge gaps

### 4.2 Pinecone Namespace Strategy for Tenant Isolation

```python
# Per-tenant namespace isolation
metadata_filter = {
    "tier_access": {"$in": tier_access_map[user_tier]}  # INTERNAL sees all, PUBLIC sees "public" only
}
```

**Advanced multi-tenancy I'd implement:**
- **Namespace-per-client** for enterprise customers (full data isolation)
- **Shared namespace with metadata filtering** for standard tiers (cost efficient)
- **Separate Pinecone indexes** for different document types (contracts vs. RFPs) to optimize embedding dimensions

### 4.3 Prompt Caching for Cost Control

```python
# Bedrock prompt caching — cache system prompts across calls
SystemMessage(
    content=SYSTEM_PROMPT,
    additional_kwargs={"cache_control": {"type": "ephemeral"}},
)
```

**Impact:** System prompts (~2K tokens) are cached for 5 minutes. With 100 queries/hour, this saves ~200K input tokens/hour → **~35% cost reduction** on Claude Sonnet.

**Advanced cost controls:**
- **Tiered model selection**: Use Claude Haiku for supervisor routing (fast, cheap), Sonnet for writer synthesis (quality)
- **Token budgets per tier**: INTERNAL gets 50K tokens, PUBLIC gets 8K — enforced at the LLM wrapper level
- **Batch embedding**: Use `embed_batch()` for ingestion instead of per-document calls — 10x cheaper

---

## 5. Stakeholder & Leadership Scenarios

### 5.1 The "Vague Ask" → Technical Spec

**Scenario:** *"We need our procurement team to ask questions about vendor data and get answers."*

**How I translated this:**

1. **Discovery session:** Asked: "What questions do they ask today? Who asks them? What data do they access?"
2. **User segmentation:** Identified three distinct user groups → mapped to INTERNAL/PARTNER/PUBLIC tiers
3. **Technical spec:**
   - Input: Natural language questions via API
   - Output: Grounded answers with citations + confidence scores
   - Safety: No raw SQL exposure to external users, PII masking for partners
   - SLAs: P95 < 8s for internal, < 4s for public (simpler queries)
4. **Prototype:** Built the supervisor-researcher graph as a working demo in 2 days, iterated based on feedback

**Key insight:** The "vague ask" actually contained a hidden multi-tenancy requirement. By asking "who asks these questions?" early, I uncovered the tier system that became a core architectural decision.

### 5.2 Mentorship & Team Practices

**Patterns I've established:**

1. **Async-first Python**: All agent nodes are `async def`, all external calls use `asyncio.to_thread()` for blocking I/O. I documented this as a team standard after finding synchronous boto3 calls blocking our event loop.

2. **Structured logging**: Enforced `structlog` with JSON output across all modules. Every agent node logs entry/exit with structured context (`tier`, `query`, `iteration_count`).

3. **Code review checklist for LLM code:**
   - [ ] Does it handle JSON parse failures gracefully?
   - [ ] Is the system prompt cached?
   - [ ] Are there iteration guards?
   - [ ] Is the output validated before returning to the user?

---

## 6. Technical Deep-Dives

### 6.1 Anthropic API vs. ChatBedrockConverse

| Dimension | Direct Anthropic API | ChatBedrockConverse |
|---|---|---|
| **Latency** | ~50ms lower (no AWS proxy) | +50ms (Bedrock routing) |
| **Guardrail integration** | Manual (separate API call) | Native `ApplyGuardrail` in same request pipeline |
| **Prompt caching** | Anthropic beta API | Bedrock supports via `anthropic_beta` flag |
| **IAM auth** | API key in env var | IAM role (no secrets to manage) |
| **Model switching** | Anthropic models only | Any Bedrock model (Claude, Llama, Mistral) |
| **Cost** | Direct pricing | Bedrock pricing (~same) |

**My choice: ChatBedrockConverse** because:
1. **Guardrail integration** — `ApplyGuardrail` runs in the same AWS region as the model, adding only ~20ms vs. a separate API round-trip
2. **IAM-based auth** — No API keys to rotate; ECS task role inherits permissions
3. **Model portability** — Can swap Claude for Llama 3 for cost-sensitive routing decisions without changing code

### 6.2 Tiered Guardrail Architecture

```
                    ┌─────────────┐
User Query ────────►│ Rate Limiter │ (slowapi · tier-aware RPM)
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ INPUT Guard  │ (Bedrock ApplyGuardrail)
                    │ - PII detect │
                    │ - Topic deny │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ Agent Graph  │ (LangGraph)
                    │ + SQL safety │ (11 patterns + table whitelist)
                    │ + Row limits │ (tier-based LIMIT injection)
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ OUTPUT Guard │ (Bedrock ApplyGuardrail)
                    │ - PII mask   │
                    │ - Grounding  │
                    └──────┬──────┘
                           ▼
                    Response to User
```

**Latency budget per tier:**

| Tier | Guardrail Strength | Added Latency | Fail Mode |
|---|---|---|---|
| INTERNAL | LOW | ~40ms | Fail-open (log + continue) |
| PARTNER | MEDIUM | ~80ms | Fail-open (log + warn) |
| PUBLIC | HIGH | ~150ms | Fail-closed (block response) |

**Key tradeoff:** For INTERNAL users, we fail-open on guardrail errors because blocking an analyst's urgent query is worse than a brief guardrail gap. For PUBLIC users, we fail-closed because data leakage risk outweighs availability.

---

## 7. Advanced Architectural Decisions for Discussion

### 7.1 I'd Add: LangGraph `Command` + Dynamic Tool Binding

Instead of hardcoded routing, use LangGraph's `Command` primitive for dynamic dispatch:

```python
from langgraph.types import Command

async def supervisor_node(state, config):
    decision = await route(state)
    if decision.parallel:
        return Command(goto=["researcher", "sql_analyst"])  # parallel fan-out
    return Command(goto=decision.next_agent, update={"sub_queries": decision.queries})
```

**Why:** This decouples routing logic from graph topology, making it testable in isolation.

### 7.2 I'd Add: Semantic Caching Layer

```python
class SemanticCache:
    """Cache answers by query similarity, not exact match."""
    
    async def get(self, query: str, threshold: float = 0.92) -> str | None:
        embedding = await embed(query)
        matches = self.index.query(vector=embedding, top_k=1)
        if matches and matches[0].score > threshold:
            return matches[0].metadata["answer"]
        return None
```

**Impact:** For repeated procurement questions ("What's our total spend?"), this skips the entire agent pipeline — reducing P50 latency from ~5s to ~200ms and cutting Bedrock costs by ~40% for common queries.

### 7.3 I'd Add: A/B Testing Framework for Prompts

```python
@experiment("supervisor_prompt_v2", traffic=0.1)
async def supervisor_node(state, config):
    prompt = get_prompt_variant(config)  # v1 or v2
    # ... rest of logic
```

Track via LangSmith tags → compare faithfulness/relevance scores between variants → promote winners.

### 7.4 I'd Add: Circuit Breaker for External Dependencies

```python
from tenacity import CircuitBreaker

pinecone_breaker = CircuitBreaker(failure_threshold=5, recovery_timeout=60)

@pinecone_breaker
async def search_docs(query):
    return await retriever.retrieve(query)
```

**Why:** If Pinecone is down, fail fast and route to web search only — don't let 5s timeouts cascade through the agent pipeline.

### 7.5 I'd Add: Structured Output with Pydantic

Replace JSON string parsing with Bedrock's structured output:

```python
from langchain_core.output_parsers import PydanticOutputParser

class SupervisorDecision(BaseModel):
    next_agent: Literal["researcher", "sql_analyst", "writer", "FINISH"]
    sub_queries: list[str]
    reasoning: str

llm = ChatBedrockConverse(...).with_structured_output(SupervisorDecision)
```

**Why:** Eliminates JSON parsing failures entirely — the LLM is constrained to produce valid Pydantic objects.

---

## 8. Questions I'd Ask the Interviewer

1. "What's your current agent topology? Supervisor-only, or do you have subagent specialization?"
2. "How do you evaluate agent quality today? LLM-as-judge, human review, or both?"
3. "What's your latency budget for agent responses? Does it vary by customer tier?"
4. "Are you using MCP in production today, or evaluating it?"
5. "How do you handle model migrations (e.g., Claude 3.5 → 4)? Do you have a canary deployment strategy?"
