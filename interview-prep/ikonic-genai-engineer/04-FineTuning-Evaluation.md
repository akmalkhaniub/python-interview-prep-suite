# 04: Fine-tuning & Model Evaluation

Fine-tuning is the process of taking a pre-trained model and training it further on a specific dataset.

## 1. When to Fine-tune?
- **Tone & Style:** Making the model sound like a specific brand.
- **Specific Knowledge:** (Rarely used for this, RAG is better).
- **Narrow Tasks:** Improving performance on a very specific output format (e.g., SQL generation).

## 2. Parameter-Efficient Fine-Tuning (PEFT)
Full fine-tuning is too expensive. Instead, we use:
- **LoRA (Low-Rank Adaptation):** Only training a small number of "adapter" weights while keeping the original model frozen.
- **QLoRA:** LoRA combined with 4-bit quantization (allows fine-tuning huge models on consumer GPUs).

## 3. The Fine-tuning Workflow
1. **Data Prep:** Clean and format data into (Instruction, Output) pairs.
2. **Training:** Using libraries like `Hugging Face TRL` or `Unsloth`.
3. **Merging:** Combining the adapter weights with the base model.

## 4. Evaluation Metrics
How do you know your fine-tuned model (or RAG system) is good?
- **G-Eval:** Using a stronger model (GPT-4) to grade the output of a smaller model.
- **Human Eval:** The gold standard, but slow/expensive.
- **Latency vs. Accuracy:** Measuring how long it takes to generate a token (tokens/sec).

## 5. Potential Interview Questions
1. **Explain the difference between Fine-tuning and RAG.**
   - *Answer:* **RAG** is like giving the model an "open book" (dynamic context). **Fine-tuning** is like "studying for an exam" (changing the model's internal knowledge/style).
2. **What is Quantization (GGUF, EXL2, AWQ)?**
   - *Answer:* Reducing the precision of model weights (e.g., from 16-bit to 4-bit) to save memory and increase speed, with minimal loss in accuracy.
3. **How do you handle "Hallucinations" in a Production Chatbot?**
   - *Answer:* 
     - **Prompting:** "If you don't know the answer, say you don't know."
     - **RAG:** Providing grounded context.
     - **Verification:** Using a second LLM pass to verify the first one's answer.
