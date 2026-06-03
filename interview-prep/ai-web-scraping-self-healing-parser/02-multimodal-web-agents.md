# Module 02: Multimodal Web Agents & Extraction

This module covers the integration of Vision-Language Models (VLMs) and LLM agent frameworks to automate web interactions, locate visual elements, and parse unstructured page layouts into structured formats.

---

## Technical Q&A

### Q1: Explain how a Multimodal Web Agent (like browser-use or Playwright-VLM integrations) translates a user prompt like "Find and download the invoice PDF" into executable browser actions.
**Answer:**
A Multimodal Web Agent operates in an **Act-Observe loop** using a Vision-Language Model (like Claude 3.5 Sonnet or GPT-4o) as the reasoning controller.

#### 1. Execution Loop:
- **Step 1: Capture State:** The runner script captures a screenshot of the current browser viewport and extracts the current DOM structure (specifically interactive elements with unique IDs, often injected via a helper script).
- **Step 2: VLM Evaluation:** The screenshot and target goal ("Find and download the invoice PDF") are passed to the VLM. To assist the model in locating elements, a **Coordinate Overlay (or bounding box IDs)** is drawn on the screenshot over all interactive nodes (buttons, links, text inputs).
- **Step 3: Action Selection:** The VLM analyzes the image, identifies the target element (e.g., a button overlay labeled `[22]`), and returns a structured tool call:
  ```json
  {"action": "click", "element_id": 22}
  ```
- **Step 4: Execution:** The runner script receives the tool call and executes it using Playwright:
  ```python
  await page.locator('[data-vlm-id="22"]').click()
  ```
- **Step 5: Repeat:** The loop repeats, capturing the new state, until the VLM determines that the PDF has been successfully downloaded and outputs an `agent_finished` state.

---

### Q2: How do you design an LLM system prompt to guarantee the output is strictly valid JSON matching a target schema? Contrast the use of raw system instructions with structured JSON mode.
**Answer:**

1. **Raw System Instructions (Text-based constraints):**
   - **How it works:** Instructing the model using prompts like: "Return only valid JSON matching this schema: `{"name": "...", "price": ...}`. Do not include markdown wraps."
   - **Vulnerability:** Brittle. Even large models occasionally prefix the output with markdown ticks (` ```json `) or append conversational text ("Here is your requested JSON:"), leading to JSON parsing errors in the backend parser.

2. **JSON Mode / Structured Outputs (Model-Native Constraints):**
   - **How it works:** Pinned at the API level (e.g., using OpenAI's `response_format={"type": "json_object"}` or Anthropic's tool-calling definitions). 
   - **Under the Hood:** During generation, the model's token selection is constrained at the decoding layer (logit bias masking). The decoder forces the model to select only tokens that satisfy the grammar rules of a valid JSON object matching the schema.
   - **Result:** 100% guarantee of valid JSON structure.

**Solidity/Python Schema Definition Example (using Pydantic with OpenAI API):**
```python
from openai import OpenAI
from pydantic import BaseModel

client = OpenAI()

class InvoiceExtraction(BaseModel):
    invoice_number: str
    amount_due: float
    due_date: str

# API call enforcing Pydantic schema validation native to the model
completion = client.beta.chat.completions.parse(
    model="gpt-4o-2024-08-06",
    messages=[
        {"role": "system", "content": "Extract invoice details from the text."},
        {"role": "user", "content": "Invoice #INV-2901 issued on 2026-05-30. Total amount: $1250.00"}
    ],
    response_format=InvoiceExtraction, # Schema constraint enforced natively
)

extracted_invoice = completion.choices[0].message.parsed
print(extracted_invoice.amount_due) # 1250.0
```

---

### Q3: How do you optimize token costs and latency when running Vision-Language Models repeatedly across web interaction loops?
**Answer:**
VLM calls are expensive because screenshots contain high amounts of token data, and processing them repeatedly at every step causes high API costs and latency.

**Optimizations:**
1. **Interactive Element Filtering (Pre-Parsing):**
   - Do not send the entire DOM tree or a raw 4K screenshot.
   - Run a client-side JS script to extract *only* interactive elements (buttons, inputs, links) and filter out passive content (such as styling, paragraphs, SVG codes). Create a lightweight representation to send as context.
2. **Dynamic Image Downscaling:**
   - Compress the screenshot to a standard size (e.g., max width of 1024px) and convert it to WebP format to minimize payload transfer size.
   - Use "Low Detail" mode if using OpenAI's API (fixed cost of 85 tokens per image) instead of "High Detail" (which parses the image into tiles of 170 tokens each).
3. **Prefix Caching (Prompt Caching):**
   - In agent loops, the system prompt and the core layout structures change very little between clicks.
   - Use APIs that support Prompt Caching (like Anthropic's prompt caching headers). By marking the system instructions and the base DOM hierarchy as cacheable, you save up to 90% of input token costs on repeating steps.
