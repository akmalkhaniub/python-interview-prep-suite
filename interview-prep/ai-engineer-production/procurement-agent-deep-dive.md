# 🎯 AI Engineer Interview Prep: Procurement Intelligence Agent

> **Project:** [procurement-intelligence-agent](https://github.com/akmalkhaniub/procurement-intelligence-agent)  
> **Stack:** LangGraph · MCP · Pinecone · Vanna AI · AWS Bedrock · Guardrails · LangSmith · FastAPI  
> 📌 = Question from JD prep material | 🔵 = Additional likely question

---

## 1. LangGraph Supervisor-Researcher Topology

### 📌 Q1.1: How do you handle long-running agent states and memory across sessions?

**Answer:**

`AgentState` uses a `TypedDict` with append-only message history via LangGraph's `add_messages` reducer. Every agent node appends its output as an `AIMessage`, creating a full audit trail. The supervisor reads the last 10 messages for context window management.

For multi-turn sessions, we use LangGraph checkpointers:
```python
from langgraph.checkpoint.postgres import PostgresSaver
graph = build_graph().compile(checkpointer=PostgresSaver(conn_string))
```

**Key design decisions:**
- **Ephemeral vs persistent state** — `iterations`, `next_agent` reset per query; `messages`, `session_id` survive across turns
- **Context window management** — Supervisor only reads last 10 messages to prevent overflow

**Advanced improvements I'd make:**
- **Sliding window + summarization agent** — Compress older messages into a summary node when context exceeds 8K tokens
- **Redis-backed checkpointer** — For horizontal scaling across ECS tasks, reduces serialization latency from ~15ms to ~3ms
- **State snapshots** — Periodically snapshot state to S3 for debugging and audit compliance

---

### 📌 Q1.2: Discuss your experience with "self-correction loops" and how you prevent agents from entering infinite loops.

**Answer — Three layers of protection:**

| Layer | Mechanism | Code |
|---|---|---|
| **Hard guard** | `max_iterations=5` — forces termination to writer | `if state["iterations"] >= state["max_iterations"]` |
| **Guardrail short-circuit** | Forbidden SQL → `guardrail_triggered=True` → skip supervisor, go to writer | Conditional edge in graph |
| **Parse failure default** | JSON extraction failure → default to `"writer"` | `json_utils.extract_json()` returns None → fallback |

**Advanced patterns I'd add:**
- **Exponential backoff on repeated routing** — If supervisor routes to `researcher` 3x with no new docs, force `writer` with low-confidence flag
- **LangGraph `interrupt_before`** — Human-in-the-loop for high-stakes queries (contracts > $1M):
  ```python
  graph = builder.compile(interrupt_before=["sql_analyst"])
  ```
- **Dead letter queue** — After max iterations, log the failed query to SQS for human review

---

### 📌 Q1.3: How do you decide when to hand off a task to a specialized agent versus keeping it in the main orchestrator?

**Answer — "Route, don't reason" principle:**

The supervisor NEVER answers directly — it only classifies intent and routes:

| Intent Signal | Route | Example |
|---|---|---|
| Needs unstructured knowledge | `researcher` | "What's our vendor onboarding policy?" |
| Needs structured data | `sql_analyst` | "Total spend by vendor this FY?" |
| Both gathered, needs synthesis | `writer` | After researcher + sql_analyst return |
| Answer validated | `FINISH` | Draft meets confidence threshold |

**When to keep in orchestrator vs dispatch:**
- **Keep** if the task is pure routing/classification (cheap, fast)
- **Dispatch** if the task requires specialized tools or domain knowledge
- **Never dispatch** for tasks that need cross-agent context (the writer needs ALL evidence)

**Advanced — Parallel dispatch:**
```python
from langgraph.types import Send

def route_supervisor(state):
    if needs_both_docs_and_data(state):
        return [Send("researcher", state), Send("sql_analyst", state)]  # fan-out
```

---

### 🔵 Q1.4: How would you handle a query that requires multiple rounds of research?

**Answer:** The supervisor already supports iterative deepening. After the researcher returns docs with avg_score < 0.65, the supervisor can re-route to researcher with refined sub-queries. The iteration counter prevents infinite loops. I'd add a **"research budget"** concept — each query gets N research rounds based on tier (INTERNAL=3, PUBLIC=1).

---

### 🔵 Q1.5: How do you test the supervisor's routing logic in isolation?

**Answer:** The routing functions (`route_supervisor`, `route_after_agents`) are pure functions that take state dicts and return strings. We test them with 9 unit tests in `test_agents.py` covering every routing branch without needing LLM calls. For integration testing, we mock the LLM to return predetermined JSON routing decisions.

---

### 🔵 Q1.6: How would you add a new agent to the graph?

**Answer:** Four steps:
1. Create `agents/new_agent.py` with `async def new_agent_node(state, config: RunnableConfig) -> dict`
2. Add the node: `builder.add_node("new_agent", new_agent_node)`
3. Add to supervisor's routing prompt + `route_supervisor` mapping
4. Add conditional edge back to supervisor: `builder.add_conditional_edges("new_agent", route_after_agents, ...)`

The graph topology is declarative — adding a node doesn't require changing existing agents.

---

## 2. Model Context Protocol (MCP)

### 📌 Q2.1: How do you version tool schemas and define "tool surfaces" for external clients?

**Answer:**

- **Server-level versioning** — FastMCP config has `version="1.0.0"`
- **Backward-compatible parameters** — New params always have defaults (e.g., `dry_run: bool = False`)
- **Breaking changes → new tool name** — `query_database_v2` rather than modifying `query_database`
- **Independent deployment** — `Dockerfile.mcp` lets us update tools without redeploying the API
- **Tool surfaces** — Each tool has rich docstrings that serve as the contract. MCP's `tools/list` endpoint lets agents discover available tools at runtime.

**Advanced — Schema registry:** I'd publish tool schemas to DynamoDB so agents can query available tools at startup, decoupling discovery from deployment.

---

### 📌 Q2.2: Why use MCP over standard REST tool-calling? Discuss latency and implementation overhead.

| Dimension | MCP | REST |
|---|---|---|
| **Protocol** | Bidirectional (server push) | Request-response |
| **Schema discovery** | Built-in `tools/list` | Manual OpenAPI |
| **Streaming** | Native SSE / streamable-http | Custom impl |
| **Agent integration** | Auto-converts via `langchain-mcp-adapters` | Manual wrapping |
| **Latency overhead** | ~5ms (protocol framing) | ~2ms |
| **Multi-model** | Same server works with any LLM | Tied to function-calling format |

**Honest take:** MCP adds ~3ms over REST, negligible vs 2-5s LLM inference. The real win is **portability** — same MCP server works with Claude, GPT-4, Gemini.

**When I'd skip MCP:** For latency-critical inner loops (tool called 50x per query), use direct function calls. Reserve MCP for the outer tool surface.

---

### 🔵 Q2.3: How does your MCP client handle server unavailability?

**Answer:** `MCPToolClient` has a graceful fallback — if the MCP server connection fails, it creates `StructuredTool` wrappers from direct Python imports. This means local dev works without running the MCP server. In production, I'd add a circuit breaker (5 failures → 60s cooldown → retry).

---

### 🔵 Q2.4: How would you add authentication to the MCP server?

**Answer:** MCP supports custom headers in the HTTP transport. I'd add JWT validation middleware to the FastMCP server and pass the user's tier claim through. The MCP client would forward the JWT from the original request context.

---

## 3. Text-to-SQL & Safety

### 📌 Q3.1: Explain your validation layer that rejects dangerous patterns.

**Three-layer defense:**

```
Layer 1: Pattern matching → 11 forbidden SQL keywords
         DROP, DELETE, TRUNCATE, ALTER, CREATE, INSERT, UPDATE, GRANT, REVOKE, EXEC, EXECUTE

Layer 2: Table whitelist → Only vendors, purchase_orders, invoices, contracts
         INFORMATION_SCHEMA explicitly blocked

Layer 3: Tier-based row limits → INTERNAL: 500, PARTNER: 100, PUBLIC: 25
         Existing LIMIT clauses are REPLACED (not appended) to prevent bypass
```

**Critical subtlety:** We REPLACE existing LIMIT clauses. If Vanna generates `LIMIT 10000`, our `_inject_limit()` rewrites to `LIMIT 25` for PUBLIC tier. This prevents data exfiltration through crafted questions.

---

### 📌 Q3.2: Discuss detecting N+1 patterns and missing indexes in LLM-generated SQL.

**N+1 Detection:**
```python
def detect_n_plus_1(sql: str) -> bool:
    """Flag queries where caller might loop over IDs without JOINs."""
    has_id_select = re.search(r"SELECT.*_id\b", sql, re.IGNORECASE)
    lacks_join = "JOIN" not in sql.upper()
    return bool(has_id_select and lacks_join)
```

**Missing index suggestion:**
- Parse WHERE/JOIN clauses, check filtered columns against known schema indexes
- Return actionable suggestions like `CREATE INDEX idx_vendor_risk ON vendors(risk_score)`

---

### 📌 Q3.3: How do you use confidence scoring before execution?

**Answer:** Currently Vanna generates SQL → validate → execute. I'd add a confidence gate:

```python
async def confidence_gate(question: str, sql: str) -> float:
    prompt = f"Rate 0-1 how correctly this SQL answers the question.\nQ: {question}\nSQL: {sql}"
    score = await llm.ainvoke(prompt)
    if score < 0.7:
        return await vanna.generate_sql(question, hint="Add JOINs for vendor names")
    return score
```

Queries scoring < 0.5 would be rejected entirely with an explanation to the user.

---

### 🔵 Q3.4: How do you handle SQL injection through natural language?

**Answer:** Vanna AI generates parameterized SQL from natural language, so there's no direct user input in SQL strings. But adversarial prompts like *"Show vendors; DROP TABLE vendors;--"* could trick the LLM. Our Layer 1 regex catches `DROP` regardless of how it appears. We also use `sqlalchemy.text()` for execution, never raw string formatting.

---

### 🔵 Q3.5: How would you handle schema changes (new columns/tables)?

**Answer:** Re-run Vanna's training harness (`train_on_schema()`) with updated DDLs. Since we store DDLs in code, schema changes go through PR review. The ChromaDB vector store is rebuilt. In production, I'd trigger retraining via a CI/CD pipeline step when migration files change.

---

## 4. RAG & Vector Strategy

### 📌 Q4.1: Discuss using LangSmith traces to drive a 40% reduction in hallucination rates.

**Eval-driven feedback loop:**

```
Production traces (LangSmith) → Sample 100/week → LLM-as-Judge scores
→ Identify low-faithfulness answers → Root-cause: retrieval gaps vs synthesis errors
→ Fix: adjust chunking, add golden examples, tune reranker threshold
```

**Concrete improvements that drove the reduction:**
1. **Multi-query expansion** (3 variants) → +25% recall
2. **Cohere reranker v3** → -30% false positives in top-5
3. **Retrieval score threshold** (< 0.65 → web search fallback) → catches knowledge gaps
4. **Tier-based metadata filtering** → eliminates information leakage

---

### 📌 Q4.2: Explain Pinecone namespaces for tenant isolation on AWS.

**Answer:**
```python
metadata_filter = {
    "tier_access": {"$in": tier_access_map[user_tier]}
    # INTERNAL sees all tiers, PUBLIC sees "public" only
}
```

**Multi-tenancy strategies:**
- **Namespace-per-client** for enterprise (full isolation)
- **Shared namespace + metadata filtering** for standard tiers (cost efficient)
- **Separate indexes** per document type (contracts vs RFPs) for optimized dimensions

---

### 📌 Q4.3: How did you use prompt caching to reduce token spend by 35%?

**Answer:**
```python
SystemMessage(
    content=SYSTEM_PROMPT,  # ~2K tokens
    additional_kwargs={"cache_control": {"type": "ephemeral"}},
)
```

System prompts cached for 5 minutes on Bedrock. With 100 queries/hour, saves ~200K input tokens/hour → **~35% cost reduction**.

**Additional cost controls:**
- **Tiered model selection** — Haiku for routing (fast/cheap), Sonnet for synthesis (quality)
- **Token budgets per tier** — INTERNAL=50K, PUBLIC=8K enforced at LLM wrapper level
- **Batch embedding** — `embed_batch()` for ingestion, 10x cheaper than per-document

---

### 🔵 Q4.4: How do you handle stale documents in the vector DB?

**Answer:** Each document has metadata with `s3_key` and ingestion timestamp. I'd add a daily reconciliation job: list S3 objects → compare against Pinecone metadata → delete vectors for removed docs → re-embed updated docs. Pinecone's `delete(filter={"s3_key": key})` makes targeted cleanup efficient.

---

### 🔵 Q4.5: What chunking strategy did you choose and why?

**Answer:** Recursive character splitting (1024 tokens, 128 overlap). Why 1024? It balances retrieval precision (smaller chunks = more specific) vs context sufficiency (larger chunks = more complete). The 128-token overlap ensures sentences aren't cut mid-thought. For contracts, I'd use a section-aware splitter that respects clause boundaries.

---

## 5. AWS Bedrock Deep-Dives

### 📌 Q5.1: Difference between direct Anthropic API vs ChatBedrockConverse — latency and guardrail integration.

| Dimension | Direct Anthropic | ChatBedrockConverse |
|---|---|---|
| **Latency** | ~50ms lower (no AWS proxy) | +50ms (Bedrock routing) |
| **Guardrails** | Separate API call | Native `ApplyGuardrail` in same pipeline |
| **Auth** | API key rotation | IAM role (no secrets) |
| **Model switching** | Anthropic only | Any Bedrock model |
| **Prompt caching** | Anthropic beta | Bedrock supports via `anthropic_beta` flag |

**My choice: ChatBedrockConverse** because:
1. Guardrail integration adds only ~20ms vs separate round-trip
2. IAM-based auth — ECS task role inherits permissions, no key rotation
3. Can swap Claude for Llama 3 for cost-sensitive routing without code changes

---

### 📌 Q5.2: Explain your tiered guardrail layer — how do you balance safety with latency budgets?

```
User Query → Rate Limiter (tier RPM) → INPUT Guard (Bedrock) → Agent Graph
→ SQL Safety (11 patterns + whitelist + row limits) → OUTPUT Guard (Bedrock) → Response
```

**Latency budget per tier:**

| Tier | Strength | Added Latency | Fail Mode | Why |
|---|---|---|---|---|
| INTERNAL | LOW | ~40ms | Fail-open | Blocking analyst's urgent query > brief guardrail gap |
| PARTNER | MEDIUM | ~80ms | Fail-open + warn | Log for audit, don't block |
| PUBLIC | HIGH | ~150ms | Fail-closed | Data leakage risk > availability |

---

### 🔵 Q5.3: How do you handle Bedrock throttling?

**Answer:** Bedrock has per-account RPM limits. I use `tenacity` retry with exponential backoff (max 3 retries, 2s initial delay). For sustained throughput, I'd request provisioned throughput for Sonnet and use on-demand for Haiku routing calls. The rate limiter at the API layer prevents user-side overload from reaching Bedrock.

---

### 🔵 Q5.4: How would you migrate from Claude 3.5 to Claude 4?

**Answer:** Since we use `ChatBedrockConverse`, the model ID is a config parameter — change `model_id` in the configurable dict. I'd do a canary deployment: route 10% traffic to Claude 4 via LangSmith tags, compare faithfulness/relevance scores for 48 hours, then promote or rollback.

---

## 6. Stakeholder & Leadership

### 📌 Q6.1: Prepare an example of translating a "vague business ask" into concrete technical specs.

**The ask:** *"We need our procurement team to ask questions about vendor data and get answers."*

**My process:**
1. **Discovery:** "What questions do they ask? Who asks them? What data do they use?"
2. **Segmentation:** Three user groups → INTERNAL/PARTNER/PUBLIC tiers
3. **Technical spec:**
   - Input: NL questions via REST API
   - Output: Grounded answers + citations + confidence scores
   - Safety: No raw SQL to external users, PII masking for partners
   - SLAs: P95 < 8s internal, < 4s public
4. **Prototype:** Working supervisor-researcher demo in 2 days, iterated from feedback

**Key insight:** The "vague ask" hid a multi-tenancy requirement. By asking "WHO asks?" early, I uncovered the tier system that became a core architectural decision.

---

### 📌 Q6.2: How have you raised the bar on team practices?

**Patterns I've established:**
1. **Async-first Python** — All agent nodes are `async def`, blocking I/O wrapped in `asyncio.to_thread()`. Documented after finding synchronous boto3 blocking event loops.
2. **Structured logging** — `structlog` JSON output across all modules. Every agent logs entry/exit with structured context.
3. **LLM code review checklist:**
   - [ ] Handles JSON parse failures gracefully?
   - [ ] System prompt cached?
   - [ ] Iteration guards present?
   - [ ] Output validated before returning?

---

### 🔵 Q6.3: How do you handle conflicting priorities between speed and safety?

**Answer:** The tier system IS the answer. INTERNAL users get fast, permissive access (fail-open guardrails, 120 RPM). PUBLIC users get slower, strict access (fail-closed, 10 RPM). This lets us serve both needs without compromising either. The tier boundary is enforced at the JWT level — no client-side override possible.

---

## 7. Advanced Architectural Decisions

### 🔵 Q7.1: Semantic Caching

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

**Impact:** For repeated queries ("total spend?"), skip entire pipeline — P50 from ~5s to ~200ms, ~40% Bedrock cost savings.

---

### 🔵 Q7.2: Structured Output (eliminate JSON parsing)

```python
class SupervisorDecision(BaseModel):
    next_agent: Literal["researcher", "sql_analyst", "writer", "FINISH"]
    sub_queries: list[str]
    reasoning: str

llm = ChatBedrockConverse(...).with_structured_output(SupervisorDecision)
```

Eliminates JSON parsing failures entirely — LLM constrained to valid Pydantic objects.

---

### 🔵 Q7.3: Circuit Breaker for External Dependencies

```python
from tenacity import CircuitBreaker
pinecone_breaker = CircuitBreaker(failure_threshold=5, recovery_timeout=60)

@pinecone_breaker
async def search_docs(query):
    return await retriever.retrieve(query)
```

If Pinecone is down, fail fast → route to web search only. Don't let 5s timeouts cascade.

---

### 🔵 Q7.4: A/B Testing for Prompts

Route 10% traffic to prompt variant via LangSmith tags → compare faithfulness/relevance → promote winner. The configurable dict in LangGraph makes this trivial without code changes.

---

### 🔵 Q7.5: Observability Beyond LangSmith

- **OpenTelemetry spans** per agent node → trace latency breakdown in Datadog/Grafana
- **Custom CloudWatch metrics** — `agent.routing.decision`, `guardrail.block.rate`, `retrieval.avg_score`
- **Cost dashboard** — Track Bedrock token spend per tier/user/day via CloudWatch Embedded Metrics

---

## 8. System Design Questions

### 🔵 Q8.1: How would you design this system for 10x traffic?

**Answer:**
- ECS auto-scaling on CPU/memory (already in CDK)
- Semantic cache layer (eliminates ~40% of Bedrock calls)
- MCP server horizontally scaled (stateless)
- Read replicas for RDS MySQL
- Pinecone serverless (auto-scales)
- API rate limiting already tier-aware

---

### 🔵 Q8.2: How would you add real-time data (live price feeds)?

**Answer:** Add a `streaming_data_agent` node that connects to WebSocket feeds via MCP. The supervisor routes market-sensitive queries there. Results are ephemeral (not stored in Pinecone) with a TTL cache.

---

### 🔵 Q8.3: How would you support multiple languages?

**Answer:** Claude natively handles multilingual. I'd add language detection in the supervisor → set response language in writer prompt → add multilingual embedding model (Cohere multilingual v3) for retrieval.

---

## 9. Coding Challenge Prep

### 🔵 Q9.1: Implement a LangGraph state reducer that deduplicates messages

```python
def dedup_messages(existing: list, new: list) -> list:
    seen = {m.content for m in existing}
    return existing + [m for m in new if m.content not in seen]
```

### 🔵 Q9.2: Write a function that safely extracts JSON from LLM output

Already implemented in `agents/json_utils.py` — 3-strategy approach: direct parse → fence strip → raw_decode.

### 🔵 Q9.3: Design a tier-aware rate limiter

Already implemented in `api/rate_limiter.py` — key by `user_id`, dynamic limit from `TierConfig.rate_limit_rpm`.

---

## 10. Questions to Ask the Interviewer

1. "What's your current agent topology? Supervisor-only or subagent specialization?"
2. "How do you evaluate agent quality today? LLM-as-judge, human review, or both?"
3. "What's your latency budget? Does it vary by customer tier?"
4. "Are you using MCP in production or evaluating?"
5. "How do you handle model migrations (Claude 3.5 → 4)? Canary strategy?"
6. "What's the biggest pain point in your current agent pipeline?"
7. "How do you handle PII in training data and traces?"
