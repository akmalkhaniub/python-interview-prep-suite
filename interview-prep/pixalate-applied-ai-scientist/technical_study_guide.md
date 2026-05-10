# Technical Study Guide: Applied AI Scientist (Pixalate)

## 1. Statistical Foundations (PhD Level)
- **Probability Distributions:** Understanding heavy-tailed distributions in traffic data (e.g., Pareto, Log-normal).
- **Hypothesis Testing:** P-values, Power analysis, and A/B testing rigor.
- **Experimental Design:** Controlling for confounding variables in observational data.
- **Model Evaluation:** Beyond Accuracy—Calibration, Precision-Recall curves, and Cost-sensitive learning for fraud.

## 2. Anomaly & Fraud Detection
- **Unsupervised Learning:** Isolation Forests, One-Class SVM, and Autoencoders.
- **Feature Engineering:** Detecting "bot-like" behavior (inter-arrival times, mouse movement patterns, user-agent entropy).
- **Graph Analytics:** Identifying fraudulent networks and coordinated clusters.

## 3. Agentic AI & Advanced Reasoning
- **Agent Orchestration:**
    - **CrewAI/AutoGen:** Managing multi-agent hierarchies for complex audits.
    - **LangGraph:** Building stateful, cyclic graphs for reasoning.
- **Prompt Engineering:** Few-shot prompting, Chain-of-Thought (CoT), and ReAct patterns for decision-making.
- **Tool Integration:** Connecting LLMs to SQL databases and external fraud-intelligence APIs.

## 4. Trust & Safety Ecosystem
- **Privacy Compliance:** Understanding COPPA (Children's Online Privacy Protection Act) and GDPR.
- **Digital Advertising:** The RTB (Real-Time Bidding) lifecycle and where fraud enters (Spoofing, Injection).
- **CTV (Connected TV):** Unique fraud vectors in Roku, Apple TV, and Fire TV environments.

## 5. Multimodal Analysis
- **Vision + Text:** Analyzing app store screenshots and descriptions to detect deceptive practices.
- **Embeddings:** Using CLIP or similar for finding "clusters" of non-compliant apps.

## 6. MLOps & Data Integrity
- **Data Quality Monitoring:** Detecting "Data Drift" and "Prior Probability Shift."
- **Feedback Loops:** Active learning strategies to improve fraud models with human-in-the-loop validation.

## Resources to Review
- [Pixalate Research Blog (Gizmodo/Washington Post cases)](https://www.pixalate.com/blog)
- [The Elements of Statistical Learning (Hastie et al.)](https://web.stanford.edu/~hastie/ElemStatLearn/)
- [LangChain: Agent Decision Making](https://python.langchain.com/docs/modules/agents/)
- [IAB Tech Lab: Ad Fraud standards (ads.txt, app-ads.txt)](https://iabtechlab.com/standards/ads-txt/)
- [Autoencoders for Anomaly Detection (Medium)](https://towardsdatascience.com/using-autoencoders-for-anomaly-detection-997232247D08)
