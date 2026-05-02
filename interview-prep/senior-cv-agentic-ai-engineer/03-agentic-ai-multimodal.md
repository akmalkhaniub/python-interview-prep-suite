# 03 - Agentic AI & Multimodal Workflows

## Agentic AI Concepts

### 1. What is an "Agent" in the context of LLMs?
**Answer:**
An LLM wrapper that can use tools, maintain memory, and make decisions about which actions to take to achieve a goal. Unlike a simple chatbot, an agent can iterate, plan, and execute multi-step workflows autonomously.

### 2. Explain the ReAct (Reason + Act) pattern.
**Answer:**
A paradigm where the agent:
1. **Thoughts:** Reasons about the current state/goal.
2. **Action:** Selects a tool to use.
3. **Observation:** Receives feedback from the tool.
4. **Repeat:** Updates its thoughts based on the observation until the goal is met.

### 3. How do you integrate Computer Vision with an Agent?
**Answer:**
- **Tool-calling:** The agent has a "tool" (e.g., `detect_objects(image_path)`) which returns a structured JSON. The agent then reasons over this JSON to answer a question.
- **Multimodal LLMs (GPTV, Claude 3, LLaVA):** The agent directly "sees" the image and describes or reasons about it.
- **Visual Programming:** The agent generates code (Python/OpenCV) to process an image and then executes that code.

## Multimodal & VLMs

### 4. What are Vision-Language Models (VLMs)?
**Answer:**
Models trained on paired image and text data.
- **CLIP (Contrastive Language-Image Pre-training):** Learns a shared embedding space for text and images. Used for zero-shot classification and image retrieval.
- **BLIP/BLIP-2:** Good for image captioning and visual question answering.
- **LLaVA:** An end-to-end trained large multimodal model that connects a vision encoder (CLIP) with an LLM (Llama).

### 5. What are common memory strategies for Agents?
**Answer:**
- **ConversationBuffer:** Full history.
- **ConversationSummary:** LLM summarizes past turns to save context window.
- **Vector Database (RAG):** Store past observations/actions in a vector DB and retrieve relevant ones based on current query.

### 6. How would you build a "Visual Scribe" agent?
**Answer:**
1. **Vision:** A module (YOLO/SAM) extracts regions of interest from a video frame.
2. **OCR/VLM:** Extracts text or describes the scene in those regions.
3. **Agent:** A LangChain agent takes these descriptions, checks a knowledge base (RAG), and generates a structured report or summary of events.
