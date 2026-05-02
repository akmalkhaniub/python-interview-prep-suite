# 02 - Alignment, Reliability & Safety

## Alignment Techniques

### 1. Difference between RLHF, PPO, and DPO?
**Answer:**
- **RLHF (Reinforcement Learning from Human Feedback):** The general paradigm of using human ratings to train a reward model, which then guides the LLM.
- **PPO (Proximal Policy Optimization):** An RL algorithm used to optimize the model based on the reward model. Complex to tune.
- **DPO (Direct Preference Optimization):** A simpler approach that optimizes the model directly on human preferences (pairs of "preferred" and "rejected" responses) without a separate reward model or RL.

### 2. What is "Constitutional AI"?
**Answer:**
A technique (pioneered by Anthropic) where a model is aligned using a set of written principles (a "Constitution") rather than direct human feedback. The model self-critiques its responses based on these rules.

### 3. Explain "Red Teaming" for LLMs.
**Answer:**
The process of intentionally trying to break the model (jailbreaking, extracting PII, generating hate speech) to identify and patch safety vulnerabilities before release.

## Reliability & Trust

### 4. How do you mitigate "Hallucinations"?
**Answer:**
- **RAG (Retrieval-Augmented Generation):** Grounding the model in external, factual data.
- **Verification Chains:** Asking the model to double-check its own logic.
- **In-context Learning:** Providing clear examples of "I don't know" responses.
- **Fine-tuning:** Training on datasets where "unsure" is a valid label.

### 5. What is "Model Monitoring" in production?
**Answer:**
Tracking performance drift, output toxicity, and user feedback. Tools like **Weights & Biases** or custom dashboards are used to visualize metrics.

### 6. Importance of "Interpretability" (Mechanistic Interpretability)?
**Answer:**
Trying to understand *why* a model makes a certain decision by looking at individual neurons or attention heads. Essential for identifying hidden biases or safety risks that don't show up in standard benchmarks.
