# Technical Study Guide: AI Orchestrator (Novo AI)

## 1. Agentic Frameworks & Orchestration
- **The Orchestrator-Workers Pattern:** Designing a primary agent that delegates sub-tasks to specialized worker agents.
- **State Management:** Using LangGraph or similar concepts to track the progress of multi-step extraction workflows.
- **Claude & Cursor Integration:** Maximizing the use of "Composer" mode and high-context windows to build entire features from a single prompt.

## 2. Evaluation & Rigor (LLM-as-a-Judge)
- **Reference-Free Evaluation:** Using a high-reasoning model (Claude 3.5 Sonnet) to judge the quality of a smaller model's output.
- **Ground Truth Mapping:** Designing automated pipelines that compare agent output against verified "Golden Datasets."
- **Spec-Driven Development:** Defining the "Source of Truth" in a Markdown spec and using AI to enforce adherence.

## 3. Medical Document Intelligence
- **Layout Awareness:** Extracting data from tables, handwritten notes, and inconsistent hospital invoice formats.
- **Entity Matching:** Resolving inconsistent medical terminology (e.g., "Cardiac Arrest" vs. "Heart Attack") using semantic embeddings.

## 4. Production Autonomy & Reliability
- **Anomaly Detection Agents:** Building "Watchdog" agents that monitor system logs and database changes for unexpected behavior.
- **Guardrails:** Implementing strict schema validation and "Dry Run" modes for autonomous database agents.
- **Problem Formulation:** Training agents to turn raw stack traces into human-readable RCA (Root Cause Analysis) reports.

## 5. BI & Hypothesis Formation
- **SQL Agentic Tool-Use:** Teaching agents to write complex SQL joins to answer questions like "Which hospitals are inflating costs for knee surgeries?"
- **Reasoning Patterns:**
    - **ReAct:** Reason then Act.
    - **Chain-of-Thought:** Encouraging agents to verbalize their hypothesis before querying the data.

## 6. Systems Thinking (Ex-Google Context)
- **Scalability:** How does your agent pipeline handle 10,000 documents per hour?
- **Decoupling:** Ensuring that an error in one agent's sub-task doesn't crash the entire orchestration loop.

## Resources to Review
- [Anthropic: Building Effective Agents](https://www.anthropic.com/news/building-effective-agents)
- [LangGraph: Multi-agent Workflows](https://python.langchain.com/docs/langgraph)
- [Cursor: Using Composer and Agentic IDEs](https://cursor.com/)
- [LLM-as-a-Judge (Paper Review)](https://arxiv.org/abs/2306.05685)
- [Medical Inflation Trends (Market Research)](https://www.willistowerswatson.com/en-US/Insights/2023/11/2024-global-medical-trends-survey-report)
