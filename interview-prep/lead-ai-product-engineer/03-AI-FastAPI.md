# 03: AI Orchestration with Claude & FastAPI

The "Core" of the role: building asynchronous Python backends to orchestrate LLM APIs.

## 1. FastAPI for AI Microservices
FastAPI is the choice here because it is **asynchronous** by nature, perfect for waiting on LLM responses without blocking.

### Example: Structured JSON Output with Pydantic
```python
from fastapi import FastAPI
from pydantic import BaseModel
import anthropic

app = FastAPI()
client = anthropic.Anthropic(api_key="my_api_key")

class ContentResponse(BaseModel):
    title: String
    body: String
    meta_description: String

@app.post("/generate")
async def generate_content(prompt: str):
    message = client.messages.create(
        model="claude-3-opus-20240229",
        max_tokens=1024,
        system="Return a JSON object matching this schema: {title, body, meta_description}",
        messages=[{"role": "user", "content": prompt}]
    )
    # Parse and return validated data
    return ContentResponse.model_validate_json(message.content[0].text)
```

## 2. Claude API Mastery
- **System Prompts:** Setting the "personality" and "constraints" of the model.
- **Context Management:** Managing the 200k+ context window effectively (don't send everything if you don't need to, save tokens).
- **Function Calling / Tool Use:** Allowing Claude to "scrape" or "search" by defining tools it can call.

## 3. Prompt Engineering for Content Pipelines
- **Chain-of-Thought:** Asking the model to "think step-by-step" for higher quality.
- **Few-Shot Prompting:** Providing examples of the desired JSON output.
- **Negative Constraints:** Explicitly telling the model what NOT to do.

## 4. Asynchronous & Long-running Tasks
- **Streaming Responses:** Using Server-Sent Events (SSE) to show the user the AI is "typing".
- **Task Queues:** Using **ARQ** or **Celery** for scraping + generation tasks that take > 30 seconds.

## 5. Potential Interview Questions
1. **How do you ensure an LLM always returns valid JSON?**
   - *Answer:* Use system prompts with strict schemas, utilize "Tool Use" (function calling) features which force JSON structure, and validate the output using Pydantic on the backend.
2. **Claude 3 vs GPT-4: When would you use one over the other?**
   - *Answer:* Claude 3 (Opus/Sonnet) has a larger context window and often follows complex instructions better. GPT-4 is sometimes better at logic/coding. For content pipelines, Claude's "writing style" is often preferred.
3. **What is "RAG" and how would you implement it for this SaaS?**
   - *Answer:* Retrieval Augmented Generation. I'd store documentation/context in a Vector Database (Pinecone/Milvus), retrieve relevant chunks based on user query, and feed them into the Claude context window.
