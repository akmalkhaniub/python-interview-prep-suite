# 03: Transformers & Deep Learning Fundamentals

The IKONIC JD requires a deep understanding of multi-layer neural networks and modern architectures.

## 1. The Evolution
- **CNN (Convolutional Neural Networks):** Best for spatial data (images). Uses filters to detect patterns.
- **RNN (Recurrent Neural Networks):** Sequential data (text). Suffers from the **Vanishing Gradient** problem (forgets early parts of long sequences).
- **LSTM (Long Short-Term Memory):** An improved RNN with "gates" to control what to remember and what to forget.
- **Transformers:** Replaced LSTMs. Uses **Attention** instead of recurrence, allowing for massive parallelization.

## 2. The Transformer Architecture
Based on the paper *"Attention Is All You Need"*.
- **Self-Attention:** Every word in a sentence looks at every other word to understand context (e.g., "bank" in "river bank" vs "bank account").
- **Multi-Head Attention:** Multiple attention mechanisms running in parallel to capture different types of relationships.
- **Positional Encoding:** Since Transformers don't process data sequentially (like RNNs), they need a way to know the *order* of words.
- **Encoder vs Decoder:** 
    - **Encoder-only:** BERT (Understanding).
    - **Decoder-only:** GPT (Generation).
    - **Encoder-Decoder:** T5, BART (Translation).

## 3. Key Concepts
- **Softmax:** Used to convert raw scores into probabilities.
- **Layer Normalization:** Keeping the values in a stable range for faster training.
- **Residual Connections:** Allowing gradients to flow through deep networks without vanishing.

## 4. Potential Interview Questions
1. **Why do we use Transformers instead of LSTMs for LLMs?**
   - *Answer:* LSTMs are sequential (slow). Transformers use self-attention, which can process the entire sequence at once (parallelization). Transformers also handle long-range dependencies much better.
2. **What is the complexity of Self-Attention?**
   - *Answer:* It is **O(n²)** where *n* is the sequence length. This is why long context windows (like 128k+) are technically challenging and expensive.
3. **Explain the "Temperature" parameter in LLMs.**
   - *Answer:* It controls the "randomness" of the Softmax output. **Low temperature (0.1)** makes the model predictable. **High temperature (0.8+)** makes it creative/random.
4. **What are "Tokenizers" and why are they important?**
   - *Answer:* LLMs don't read text; they read numbers. Tokenizers split text into sub-words (BPE - Byte Pair Encoding). How a model tokenizes text affects its performance (e.g., handling whitespace or emojis).
