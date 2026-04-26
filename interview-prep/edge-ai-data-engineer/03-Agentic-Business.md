# 03: Agentic AI for Business (CrewAI / LangGraph)

Edge wants to automate business functions (Sales, HR, Compliance) using "Agentic AI".

## 1. CrewAI: Multi-Agent Collaboration
CrewAI is great for "Role-playing" tasks.
- **Agents:** Define a Role (e.g., "Market Researcher"), a Goal, and a Backstory.
- **Tasks:** Specific objectives (e.g., "Analyze this company's health").
- **Process:** Sequential, Hierarchical, or Consensual.

### Example: Outbound Sales Workflow
1. **Agent 1 (Researcher):** Scrapes the web for recent news about a prospect.
2. **Agent 2 (Writer):** Drafts a personalized email based on the news.
3. **Agent 3 (Reviewer):** Checks the email against brand guidelines.

## 2. LangGraph for Workflows
Use LangGraph when the process is **Stateful** and **Cyclic**.
- **Example:** A compliance agent that keeps checking a document until all required fields are filled.

## 3. Tool Use (Function Calling)
To be useful, agents must "do" things.
- **Salesforce Tool:** `create_lead(name, email)`.
- **Slack Tool:** `send_alert(channel, message)`.
- **Search Tool:** `tavily_search(query)`.

## 4. Evaluation of Agents
- **Traceability:** Using **LangSmith** or **Arize Phoenix** to see every step an agent took.
- **Success Rate:** What % of agent runs resulted in a successfully sent email or created lead?

## 5. Potential Interview Questions
1. **How do you handle "Agent Hallucinations" in an HR or Compliance context?**
   - *Answer:* Use RAG with a strict "source-only" prompt. Implement a "Reviewer Agent" to fact-check the output. Use human-in-the-loop for high-stakes decisions.
2. **Why use CrewAI over a single LLM prompt?**
   - *Answer:* Single prompts struggle with complex, multi-step reasoning. Multi-agent systems break the problem down, leading to higher quality and better "specialization" for each part of the task.
3. **Describe how you'd build a "Client Health Tracking" agent.**
   - *Answer:* An agent that periodically queries BigQuery for a client's usage data -> queries HubSpot for recent support tickets -> uses an LLM to "score" the health -> posts a summary in Slack if the score is low.
