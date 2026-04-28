# 04: NLP, Evaluation, and Observability

Moving beyond "it looks okay" to quantitative metrics and monitoring.

## 1. Traditional NLP Tasks
*   **Classification**: Routing queries (e.g., is this a "Sales" or "Support" question?).
*   **NER (Named Entity Recognition)**: Extracting IDs, Names, or Dates from documents to use as metadata filters.
*   **Summarization**: Condensing long enterprise reports into executive summaries.

## 2. Evaluation Metrics (The "RAG Triad")
Evaluating RAG requires checking three relationships:
1.  **Faithfulness (Groundedness)**: Is the answer based *only* on the retrieved context? (Avoids hallucinations).
2.  **Answer Relevance**: Does the answer actually address the user's question?
3.  **Context Precision**: Are the retrieved documents actually relevant to the query?

### Tools
*   **RAGAS**: A framework for automated evaluation of RAG pipelines using LLMs as judges.
*   **G-Eval**: Using GPT-4 with specific chain-of-thought prompts to score outputs.

## 3. Observability & Tracing
*   **LangSmith / Arize Phoenix**: Visualizing the trace of an LLM call. Which chunk was retrieved? What was the exact prompt sent? How long did each step take?
*   **Azure Monitor & Application Insights**: Logging token usage, latency, and error rates for production monitoring.

## 4. Prompt Engineering Strategies
*   **Few-Shot Prompting**: Providing 2-3 examples of the desired input/output.
*   **Chain-of-Thought (CoT)**: Asking the model to "think step by step" to improve reasoning.
*   **System Messages**: Defining clear personas and constraints for the LLM.

## Interview Questions
1.  **"How do you evaluate if your RAG system is hallucinating?"**
    *   *Ans*: Use Groundedness evaluation. Compare the LLM's answer against the retrieved context. If info exists in the answer but not in context, it's a hallucination.
2.  **"What are the trade-offs of using an LLM to evaluate another LLM?"**
    *   *Ans*: *Pros*: Fast, scalable, consistent. *Cons*: Might have bias toward its own style, can be expensive at scale, and might miss subtle factual errors if not prompted correctly.
3.  **"Explain how you would use NER in a document extraction pipeline."**
    *   *Ans*: I would use it to extract structured fields (like Invoice Number, Amount, Date) from unstructured text, which can then be used to populate a database or filter searches.

## Practice Task
*   Define a small evaluation set of 5 questions/answers. Use an LLM to score another LLM's response based on a "Helpfulness" scale of 1-5.
