# 03 - Prompt Engineering & Fine-Tuning

## Prompt Engineering

### 1. What is "Few-Shot Prompting"?
**Answer:**
Providing a few examples of input-output pairs within the prompt to show the model the desired format or reasoning style.

### 2. Explain "Chain-of-Thought" (CoT) prompting.
**Answer:**
Asking the model to "think step-by-step." This forces the model to generate intermediate reasoning steps, which significantly improves performance on complex logic or math problems.

### 3. What is "Self-Consistency" prompting?
**Answer:**
Generating multiple CoT paths for the same query and then taking a majority vote on the final answer.

## Fine-Tuning

### 4. When should you choose Fine-Tuning over RAG?
**Answer:**
- **RAG:** For dynamic, vast, or private knowledge that changes frequently. 
- **Fine-Tuning:** For teaching the model a specific **style**, **tone**, or **complex structured output** (e.g., specific SQL dialect) that can't be fully captured in a prompt. It is NOT good for teaching new facts.

### 5. What is LoRA (Low-Rank Adaptation)?
**Answer:**
A Parameter-Efficient Fine-Tuning (PEFT) technique. Instead of updating all billions of parameters, LoRA adds small, low-rank matrices to the model layers and only trains those. This reduces memory usage by >10,000x and allows fine-tuning on consumer GPUs.

### 6. What is RLHF (Reinforcement Learning from Human Feedback)?
**Answer:**
A process to align LLMs with human values.
1. **SFT:** Supervised Fine-Tuning on high-quality examples.
2. **Reward Model:** Humans rank model outputs, and a separate model learns those preferences.
3. **PPO:** The LLM is optimized to maximize the score from the Reward Model.
