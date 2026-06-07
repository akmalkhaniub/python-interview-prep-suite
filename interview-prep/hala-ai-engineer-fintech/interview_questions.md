# Interview Questions & Expert Answers: AI Engineer (HALA Fintech)

This document contains targeted technical interview questions and high-level expert answers designed for the AI Engineer role at HALA, focusing on fintech machine learning pipelines, LLM fine-tuning, RAG over structured databases, and compliance-driven data handling.

---

## Part 1: Tabular Machine Learning & Transaction Classification

### 1. How would you design a machine learning pipeline to automatically classify merchant transaction descriptions into standardized financial categories?
**Expert Answer:**
"Transaction descriptions are often short, noisy, and unstructured (e.g., 'DB* AMZN MKTP US*1A2B'). I would approach this with a hybrid classification pipeline:
1. **Data Pre-processing:** Normalize descriptions by stripping special characters, removing non-alphabetic transaction noise, and tokenizing merchant names.
2. **Feature Extraction:**
   - **Textual Features:** I use TF-IDF or pre-trained sentence transformers (like `all-MiniLM-L6-v2`) to extract semantic embeddings from description text.
   - **Structured Metadata:** Extract transaction amount, time-of-day, day-of-week, and location details (MCC - Merchant Category Code) as numerical/categorical features.
3. **Model Selection:**
   - For a fast baseline, I use **XGBoost** trained on combined TF-IDF and metadata vectors.
   - For higher accuracy, I use a PyTorch-based neural network combining a fine-tuned DistilBERT head (for the textual description) with a multi-layer perceptron (for tabular features like transaction amount).
4. **Post-processing:** Implement a deterministic lookup table for known merchant patterns (e.g., 'NETFLIX' maps directly to 'Subscription/Entertainment') to bypass inference latency for obvious matches."

### 2. Financial transaction datasets (like fraud detection or specific SME expense groups) are heavily imbalanced. How do you handle class imbalance in your models?
**Expert Answer:**
"Class imbalance causes models to over-predict the majority class. I address this using a combination of data-level and algorithmic techniques:
1. **Algorithmic Adjustments (Preferred):**
   - **Class Weights:** In PyTorch's `CrossEntropyLoss` or XGBoost's `scale_pos_weight`, I assign higher loss weights to minority classes, forcing the optimizer to penalize minority misclassifications more heavily.
   - **Focal Loss:** Use Focal Loss to dynamically scale the loss based on prediction confidence, forcing the model to focus on hard-to-classify examples (which minority classes usually are) rather than easy majority ones.
2. **Data-Level Resampling:** Use **SMOTE (Synthetic Minority Over-sampling Technique)** or ADASYN to synthetically generate new minority class samples, combined with **Tomek Links** undersampling to clean decision boundaries.
3. **Evaluation Metrics:** I never use raw accuracy. Instead, I optimize for **F1-Score**, **Precision-Recall AUC (PR-AUC)**, and check the confusion matrix to ensure minority class recall is high."

---

## Part 2: Generative AI, Fine-Tuning & Financial RAG

### 3. Explain how you would fine-tune an open-weights model (like Llama-3-8B) using Parameter-Efficient Fine-Tuning (PEFT/LoRA) for a specialized financial text analysis task.
**Expert Answer:**
"Fine-tuning a full 8B model is computationally expensive. I use QLoRA (Quantized Low-Rank Adaptation) to run fine-tuning on a single consumer GPU:
1. **Quantization:** Load the base model in 4-bit precision using `BitsAndBytesConfig` (NF4 quantization) to minimize GPU memory consumption.
2. **LoRA Configuration:** Define low-rank adapters. We inject small, trainable rank-decomposition matrices into the self-attention weights (specifically target modules `q_proj`, `v_proj`, `k_proj`, and `o_proj`).
   - We set a low rank (e.g., $r = 8$ or $16$) and alpha (scaling parameter, e.g., $\alpha = 32$), keeping 99% of the base model weights frozen.
3. **Data Formatting:** Structure the training data in instruction-response pairs (e.g., inputting a financial report and expecting a structured JSON risk analysis).
4. **Training:** Use Hugging Face's `SFTTrainer` (Supervised Fine-Tuning Trainer) with gradient checkpointing and a cosine learning rate scheduler.
5. **Merging:** After training, I merge the adapter weights back into the base model or serve them dynamically using a multi-adapter engine like LoRAX to save hosting costs."

### 4. How would you build a RAG system that allows an LLM to accurately query a relational SQL database containing transaction records (Text-to-SQL)?
**Expert Answer:**
"Directly exposing SQL databases to LLMs requires strict constraints to prevent syntax errors and security exploits:
1. **Schema Retrieval (Context):** I extract the database schema (DDL statements) and write them into the system prompt, alongside few-shot examples showing natural language queries mapped to valid SQL statements.
2. **gRPC/REST Isolation:** The LLM does not execute SQL directly. The LLM generates a SQL query inside a text block.
3. **Parsing & Validation:** In our Python layer, I parse the generated SQL, validate it against an AST (Abstract Syntax Tree) analyzer to ensure it is a read-only query (`SELECT` only), and prevent SQL injection or schema modifications.
4. **Database Execution:** Execute the SQL query against a read-only replica database, format the returned tabular rows as markdown or JSON, and pass the data back to the LLM.
5. **Synthesized Answer:** The LLM reads the raw data rows and synthesizes a natural language response for the user (e.g., 'You spent a total of 1500 AED on software subscriptions last month')."

---

## Part 3: Fintech Compliance & Security

### 5. Fintech applications have strict regulatory constraints regarding customer data privacy. How do you protect PII (Personally Identifiable Information) before sending data to external APIs like OpenAI?
**Expert Answer:**
"To ensure compliance with GDPR and local financial authorities, customer PII must never leave our secure VPC. I implement a local sanitization gateway:
1. **Regex & NER Masking:** Before formatting a prompt, the raw text is passed through a local Named Entity Recognition (NER) pipeline (using Presidio or SpaCy) combined with regex filters to detect names, phone numbers, email addresses, credit cards, and bank account numbers.
2. **Tokenization/Placeholder Replacement:** Masked entities are replaced with unique tokens (e.g., 'My name is John Doe' becomes 'My name is [PERSON_1]'). I maintain a secure, local lookup map (`[PERSON_1] -> John Doe`) cached in Redis for the duration of the session.
3. **API Submission:** The completely sanitized prompt is sent to the external LLM API.
4. **De-anonymization:** When the response is received from the LLM, the local gateway replaces the tokens with the original values from the Redis lookup map before rendering the output to the client. This ensures the model receives cohesive context without ever exposing raw customer identifiers."
