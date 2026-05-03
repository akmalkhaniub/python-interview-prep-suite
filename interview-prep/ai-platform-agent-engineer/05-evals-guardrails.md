# 05. Production Evals & Guardrails

To "own" a production agent platform, you must ensure it is safe and its performance is measurable.

## 🛡️ Guardrails: Tiered Access
The JD mentions "tier-based access layers" (internal, partner, public).

### 1. AWS Bedrock Guardrails
- **Content Filters:** Built-in protection against hate speech, insults, and sexual content.
- **Denied Topics:** Specific topics the agent should never discuss (e.g., "competitor pricing" or "legal advice").
- **PII Redaction:** Automatically masking Social Security Numbers, emails, etc., before the model sees the data.
- **Regex Filters:** Hard-coded patterns to block specific strings or API keys.

### 2. Tiered Latency & Safety Budgets
- **Public Tier:** High safety (Bedrock Guardrails + Custom Classifier), potentially higher latency due to multiple checks.
- **Internal Tier:** Lower safety overhead, optimized for speed and internal "power users."

## 📊 Evaluation (LangSmith)
"Lead evaluation and observability" is a core success metric.

### 1. Tracing
- **LangSmith:** Use it to visualize the agent's "thinking" process. Find where a tool call failed or where the prompt became too long.
- **Metadata Tagging:** Tagging traces with `user_id`, `tier`, and `model_version` to compare performance across cohorts.

### 2. Eval Harnesses
- **LLM-as-Judge:** Using a more powerful model (e.g., Claude 3 Opus) to rate the responses of a faster model (e.g., Claude 3.5 Sonnet).
- **Golden Datasets:** A curated set of (Question, Context, Answer) triplets used to run automated regression tests.
- **A/B Testing:** Routing 10% of traffic to a new prompt version and measuring the "Helpfulness" score in LangSmith.

## ❓ Common Interview Questions
1. **How do you calibrate an "LLM-as-judge"?**
   - *Answer:* You compare its ratings against a set of human-rated responses. If the LLM judge is too lenient or too strict, you adjust its system prompt (rubric) until it aligns with human ground truth.
2. **What is the "ApplyGuardrail API"?**
   - *Answer:* It's a Bedrock API that allows you to run a piece of text through a guardrail without invoking a model. This is useful for pre-processing user input.
3. **Difference between a "Hard" and "Soft" guardrail?**
   - *Answer:* A **Hard** guardrail immediately stops the process (e.g., a regex filter). A **Soft** guardrail might just tag the response for review or ask the model to rephrase (e.g., a "refusal" response).

---
**Next Step:** See it all in action in the [06-agent-platform-demo.ipynb](06-agent-platform-demo.ipynb).
