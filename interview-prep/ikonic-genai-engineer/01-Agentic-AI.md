# 01: Agentic AI & LangGraph

Agentic AI refers to systems where an LLM is given "agency" to make decisions, use tools, and loop until a goal is reached.

## 1. What is LangGraph?
LangGraph is an extension of LangChain designed for building **cyclic** graphs.
- **Nodes:** Functions or agents that perform work.
- **Edges:** Logic that determines which node to visit next (conditional edges).
- **State:** A shared object that persists across node executions.

## 2. Multi-Agent Systems
Instead of one "God Agent", you build specialized agents:
- **Researcher Agent:** Scrapes and finds info.
- **Writer Agent:** Drafts content.
- **Reviewer Agent:** Critiques and sends back for fixes.
- **Orchestrator:** Decides which agent works next.

## 3. Tool Calling (Function Calling)
How agents interact with the real world.
- **Schema:** Defining the tool name, description, and parameters in JSON Schema.
- **Error Handling:** What happens if the tool returns an error? The agent should be able to "read" the error and retry.

## 4. Persistent Memory
- **Short-term:** Thread-based history (ChatHistory).
- **Long-term:** Storing user preferences or summaries in a database/vector store.
- **LangGraph Checkpoints:** Saving the state of a graph so it can be resumed later.

## 5. Potential Interview Questions
1. **How is LangGraph different from a standard LangChain Agent?**
   - *Answer:* Standard agents are often black boxes. LangGraph gives you "fine-grained control" over the flow. You can define loops, conditional exits, and human-in-the-loop approvals more easily.
2. **Explain "Plan-and-Execute" vs "ReAct" patterns.**
   - *Answer:* **ReAct** (Reason + Act) is a step-by-step loop. **Plan-and-Execute** first creates a full plan and then executes it, which is often more reliable for complex tasks.
3. **How do you prevent an agent from getting stuck in an infinite loop?**
   - *Answer:* Set a `recursion_limit` in the graph configuration and implement logic to detect repeated identical actions.
4. **How would you implement a "Human-in-the-loop" approval step in LangGraph?**
   - *Answer:* Use an `interrupt` or a conditional edge that points to an "Awaiting Approval" state, which requires external input to transition to the next node.
