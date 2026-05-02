# 04 - Evaluation Frameworks & Novel Evals

## Evaluation Strategy

### 1. What is "LLM-as-a-Judge"?
**Answer:**
Using a strong model (like GPT-4) to evaluate the outputs of a weaker or experimental model. It's faster and cheaper than human evaluation but can suffer from "Self-preference bias" and "Length bias."

### 2. How to detect and prevent "Benchmark Contamination"?
**Answer:**
Checking if the test set data was accidentally included in the training data. 
- **Prevention:** Using N-gram overlap checks, fuzzy matching, and held-out internal benchmarks that are never made public.

### 3. Difference between MMLU, HumanEval, and GSM8K?
**Answer:**
- **MMLU:** Massive Multitask Language Understanding (General knowledge across 57 subjects).
- **HumanEval:** Coding performance (Python functions).
- **GSM8K:** Grade school math word problems (Reasoning).

## Novel Evals

### 4. How do you evaluate "Reasoning" capabilities?
**Answer:**
Using **Chain-of-Thought (CoT)** prompting and checking not just the final answer, but the logical steps taken. Evals like **MATH** or **Big-Bench Hard** are designed for this.

### 5. Importance of "Adversarial Evaluation"?
**Answer:**
Testing how the model performs on "Edge cases" or "Trick questions" designed to expose its failure modes.

### 6. What is "Elo Rating" in model evaluation?
**Answer:**
A system to rank models based on pairwise comparisons (Model A vs. Model B), similar to chess rankings. Standardized by platforms like **LMSYS Chatbot Arena**.
