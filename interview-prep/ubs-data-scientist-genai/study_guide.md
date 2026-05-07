# Technical Study Guide: Data Scientist (UBS Wealth Management)

## 1. Advanced RAG & Vector Databases

### Retrieval-Augmented Generation (RAG)
- **Indexing:** Chunking strategies (Recursive, Semantic), Embedding models (OpenAI, Hugging Face, Cohere).
- **Retrieval:** Vector search, Hybrid search (Keyword + Vector), Re-ranking (Cross-encoders).
- **Generation:** Context window management, citation/grounding to prevent hallucinations.
- **Tools:** Pinecone, Milvus, Weaviate, or FAISS.

### RAG Evaluation (Crucial for Finance)
- **Metrics:** Faithfulness, Answer Relevance, Context Precision (RAGAS framework).
- **Frameworks:** DeepEval, TruLens.

## 2. Agentic AI & Reasoning

### Reasoning Agents
- **Concepts:** Chain of Thought (CoT), ReAct (Reasoning and Acting) patterns.
- **Frameworks:** LangGraph, CrewAI, AutoGen.
- **Tool Use:** Teaching LLMs to use external APIs (e.g., Bloomberg, internal portfolio databases) securely.

### Feedback Loops
- **Reinforcement Learning from Human Feedback (RLHF):** Collecting advisor feedback to improve agent responses.

## 3. MLOps & Responsible AI in Finance

### MLOps Lifecycle
- **Version Control:** DVC (Data Version Control), MLflow for experiment tracking.
- **Deployment:** Blue-green deployments for models, API design (REST/gRPC).
- **Monitoring:** Detecting drift (Concept/Data drift) in financial market data.

### Responsible AI & Compliance
- **Explainability:** Using SHAP/LIME for traditional ML; Model cards for GenAI.
- **Data Privacy:** Differential Privacy, K-anonymity in financial datasets.
- **Governance:** Aligning with MAS (Monetary Authority of Singapore) guidelines on AI (FEAT principles).

## 4. Machine Learning Foundations

### Deep Learning
- **Architectures:** Transformers (Attention mechanism), CNNs/RNNs (for time-series financial data).
- **Frameworks:** PyTorch (distributed training), Hugging Face Transformers.

### Traditional ML
- **Boosting:** XGBoost, LightGBM (standard for tabular data like credit scoring or churn prediction).
- **Evaluation:** Precision-Recall curves, F1-score (balancing false positives in financial fraud).

## 5. Potential Interview Questions

### Technical (GenAI Focus)
1. "How would you build a RAG system that only retrieves information the user is authorized to see?"
2. "Explain the difference between fine-tuning a model and RAG. When would you use one over the other for a wealth management advisor tool?"
3. "How do you handle hallucinations in an agentic workflow that performs financial calculations?"
4. "Describe a strategy for monitoring an LLM-based application in production."

### Behavioral & Domain
1. "UBS is a highly regulated environment. How do you balance the 'move fast' nature of AI development with compliance requirements?"
2. "Describe a time you translated a complex business problem in finance into a machine learning solution."
3. "How do you stay up-to-date with the rapidly evolving LLM landscape?"
