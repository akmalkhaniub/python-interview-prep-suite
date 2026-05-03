# 03. Rigorous Evaluation & Benchmarking

"Develop benchmarks and evaluation frameworks" is a key responsibility for this research role.

## 📏 Core Evaluation Metrics
- **Perplexity (PPL):** Measures how well a model predicts a sample. Lower is better. Good for pre-training progress but doesn't capture "helpfulness."
- **NLL (Negative Log Likelihood):** The underlying loss for PPL.
- **Accuracy @ k:** Common in coding benchmarks (HumanEval) where you check if the model gets the right answer in $k$ attempts.

---

## 🏆 Standard Benchmarks to Know
As a researcher, you should know the flaws and strengths of these:
- **MMLU (Massive Multitask Language Understanding):** Tests general knowledge across 57 subjects. Criticized for "benchmark leakage" where data is present in training sets.
- **GSM8K:** Grade school math word problems. Tests multi-step reasoning.
- **HumanEval / MBPP:** Python coding tasks.
- **Chatbot Arena (Elo):** Crowdsourced human comparisons. The current "gold standard" for real-world helpfulness.

---

## 🏗️ Designing Novel Benchmarks
When a model improves, existing benchmarks become "saturated" (models score 90%+). A researcher must build the *next* benchmark.

### 1. Contamination Detection
- How do you know the model didn't "memorize" the benchmark?
- **Technique:** n-gram overlap analysis between training corpus and eval set.
- **Technique:** "Canary tokens" embedded in data to detect if it was scraped.

### 2. LLM-as-a-Judge
Using a "Gold" model (e.g., Claude 3 Opus) to evaluate a candidate model.
- **The Rubric:** Defining precise dimensions (Correctness, Helpfulness, Tone).
- **Bias Mitigation:** LLM judges have "position bias" (preferring the first option) and "length bias" (preferring longer answers). You must swap orders and normalize scores.

### 3. Static vs. Dynamic Evals
- **Static:** Fixed set of Q&A.
- **Dynamic:** A living eval set that is updated or procedurally generated to prevent leakage.

---

## ❓ Research Interview Questions
1. **Explain "Length Bias" in LLM evaluation.**
   - *Answer:* LLM judges often equate "more text" with "more effort/quality." This can be mitigated by strict word count constraints in the rubric or by using reference answers of similar lengths.
2. **How would you evaluate if a model has "Reasoning" capabilities vs. "Pattern Matching"?**
   - *Answer:* Use "Counterfactual Evals"—change a small, non-essential part of a math problem (e.g., instead of base-10, use base-8). If the model fails, it was likely just pattern matching common internet text.
3. **What is a "Pareto Frontier" in the context of Model Research?**
   - *Answer:* It's the set of models that offer the best tradeoff between two competing metrics (e.g., Model Size vs. Accuracy, or Accuracy vs. Latency).

---
**Next Step:** See the infrastructure required to run these experiments in [04-pytorch-distributed-training.md](04-pytorch-distributed-training.md).
