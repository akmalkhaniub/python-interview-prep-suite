# 02. Model Alignment: RLHF, DPO, & Steering

Alignment research focuses on ensuring models follow instructions, remain safe, and align with human preferences.

## 🔄 The RLHF Pipeline
The standard method for aligning frontier models (GPT-4, Claude, Llama 3).

1. **SFT (Supervised Fine-Tuning):** Fine-tuning the base model on a high-quality dataset of (Prompt, Response) pairs.
2. **Reward Modeling (RM):** 
   - Collect human preferences (Model A vs. Model B).
   - Train a separate "Reward Model" to predict which response humans prefer.
3. **PPO (Proximal Policy Optimization):**
   - Use Reinforcement Learning to update the model to maximize the reward from the RM.
   - Use a **KL-Divergence penalty** to ensure the model doesn't drift too far from the original SFT version (to prevent "reward hacking").

---

## ⚡ DPO: Direct Preference Optimization
The JD mentions "novel methods." DPO is the most significant recent shift in alignment research.

- **The Idea:** You don't need an explicit Reward Model or the complexity of PPO.
- **The Math:** DPO derives a closed-form solution that allows you to optimize the policy model directly on preference data using a simple cross-entropy loss.
- **Advantages:** More stable, easier to scale, and computationally cheaper than RLHF.

---

## 🛡️ Constitutional AI & Steering
- **Constitutional AI (Anthropic):** Training models with a "constitution" (a set of written principles). The model critiques its own responses based on these rules.
- **Representation Engineering (RepE):** Research into "steering" models by manipulating their internal activations (e.g., making a model "more honest" by finding the honesty direction in its latent space).
- **Jailbreaking Research:** Identifying adversarial prompts that bypass safety filters to better understand model vulnerabilities.

---

## ❓ Research Interview Questions
1. **What is "Reward Hacking" and how do you prevent it?**
   - *Answer:* It's when the model finds a way to get a high reward score without actually being helpful (e.g., adding "I am a good model" to every answer). It's prevented using KL-Divergence constraints and high-quality, diverse reward data.
2. **Compare PPO and DPO.**
   - *Answer:* PPO is an online RL method that requires maintaining multiple models in memory (Policy, Reference, Reward, Value). DPO is an offline method that simplifies this into a single optimization step, though it may be more sensitive to data quality.
3. **How does "Self-Correction" work in research?**
   - *Answer:* You train a model to identify its own errors (reasoning or factual) by providing it with "Chain-of-Thought" examples of error detection and correction.

---
**Next Step:** Learn how to measure these capabilities in [03-eval-frameworks-benchmarking.md](03-eval-frameworks-benchmarking.md).
