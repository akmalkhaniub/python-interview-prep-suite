# Deep Dive: LLM Fine-Tuning & Optimization

LineSlip specializes in extracting data from long, complex insurance documents, which requires highly optimized LLMs.

## 1. Parameter-Efficient Fine-Tuning (PEFT)
*   **LoRA (Low-Rank Adaptation):** Freezing the pre-trained model weights and injecting trainable rank decomposition matrices into each layer of the Transformer architecture.
*   **Benefits:** Reduces the number of trainable parameters by 10,000x and GPU memory requirements by 3x.
*   **Scenario:** Fine-tuning Llama-3 for "Insurance Clause Identification" using only 16GB of VRAM.

## 2. Optimization with Unsloth
*   **What is Unsloth?** A wrapper around the Hugging Face ecosystem that optimizes the "Manual Backprop" and "Kernels" to make fine-tuning 2-5x faster.
*   **Key Features:** Support for 4-bit quantization (bitsandbytes) and automatic Flash Attention integration.
*   **Performance:** It allows you to train larger models (like 70B) on hardware previously restricted to 7B or 13B models.

## 3. Handling Large Context Windows
*   **The Insurance Challenge:** Policies can be hundreds of pages. Standard 4k context windows aren't enough.
*   **Solutions:**
    - **Long-Context Models:** Fine-tuning models like Qwen or Llama2-Long that support 32k-128k context windows.
    - **Flash Attention 2:** Improving the speed and memory efficiency of the attention mechanism.
    - **Sliding Windows:** Breaking documents into overlapping chunks and summarizing them before the final extraction.

## 4. Model Evaluation (Perplexity & F1)
*   **Perplexity:** Measuring how well the model predicts the "next token" in the insurance domain.
*   **F1-Score (Extraction):** Measuring the accuracy of extracting specific entities (e.g., "Premium Amount," "Effective Date") against a ground-truth dataset.

## Interview Questions
1.  "Explain the mathematical intuition behind LoRA. Why does it work without updating all weights?"
2.  "How do you handle 'Catastrophic Forgetting' when fine-tuning a general LLM for a specific domain like commercial insurance?"
3.  "Walk me through the steps you would take to fine-tune a model using Unsloth on a single A100 GPU."
4.  "What are the trade-offs between '4-bit' and '16-bit' fine-tuning in terms of accuracy and speed?"
