# Technical Study Guide: Lead Data Scientist (Hedge Fund)

## 1. AI for Equity Research & Alpha

### Unstructured Data Processing (NLP for Finance)
- **Financial Document Extraction:** Using LLMs to parse complex tables and footnotes in 10-Ks/10-Qs.
- **Sentiment Analysis:** Going beyond "Positive/Negative" to "Nuance detection" in management tone during earnings calls.
- **Tools:** LlamaIndex (for multi-document RAG), Unstructured.io, LayoutLM.

### Signal Generation
- **Statistical Arbitrage:** Understanding the basics of mean reversion and momentum.
- **Feature Engineering:** Creating signals from alternative data (e.g., supply chain data, job postings).
- **Backtesting:** Evaluating AI-driven signals for Sharpe ratio, drawdown, and information coefficient (IC).

## 2. Agentic Workflows in Finance

### Agent Architectures
- **Planning Agents:** Decomposing a research task (e.g., "Analyze the impact of a new Chinese regulation on the semiconductor sector").
- **Multi-Agent Systems:** One agent for data retrieval, one for financial analysis, and one for adversarial critique (checking for biases).
- **Guardrails:** Ensuring the agent doesn't hallucinate "alternative facts" in financial reports.

### Cross-Lingual Capabilities
- **Mandarin NLP:** Handling simplified vs. traditional Chinese, regional financial terminology, and news sources (WeChat, Caixin, etc.).
- **Translation vs. Direct Processing:** Evaluating when to translate Chinese documents to English vs. processing them in native LLMs (like Qwen or GLM).

## 3. Engineering for High-Performance Finance

### Data Pipelines
- **Real-time vs. Batch:** Building low-latency pipelines for news sentiment vs. batch pipelines for historical filings.
- **Reliability:** Handling data quality issues (missing values, corporate actions, survivorship bias).

### Stack
- **Python:** High-performance libraries (Polars, NumPy, Numba).
- **Deployment:** Containerization (Kubernetes), API performance, and cloud-native scaling (AWS/GCP).

## 4. Market & Strategy Understanding

### Long/Short Equities
- **Fundamental Analysis:** How AI can speed up "Bottom-up" research.
- **Quantitative Overlays:** Using AI to filter or rank fundamental ideas.
- **Short Selling:** Identifying red flags in balance sheets or management turnover.

## 5. Potential Interview Questions

### Technical (Hedge Fund Specific)
1. "How would you build an agentic system to monitor the Chinese semiconductor supply chain using both news and custom data?"
2. "How do you measure the 'Alpha' of an LLM-based insight tool?"
3. "Explain how you would handle 'look-ahead bias' when training a model on historical earnings call transcripts."
4. "Describe a multi-agent system that critiques a Portfolio Manager's investment thesis."

### Commercial & Domain
1. "A PM says their intuition contradicts your model's signal. How do you handle this?"
2. "Why is Mandarin fluency critical for alpha generation in a global hedge fund today?"
3. "Describe a time you delivered a tool that directly led to a successful trade or avoided a major loss."
