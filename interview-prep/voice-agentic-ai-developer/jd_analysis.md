# Job Description Analysis: Agentic AI Developer (Voice)

## Role Overview
**Position:** Agentic AI Developer
**Focus:** Production-grade Voice-First Calling Agents.
**Core Mission:** Building autonomous agents that can navigate phone calls (inbound/outbound), handle complex human interactions, and complete tasks end-to-end (booking, lead gen).

## Core Competencies

### 1. The Voice AI Stack
- **STT (Speech-to-Text):** Deepgram, AssemblyAI.
- **LLM (Brain):** Claude, GPT, Gemini (Tool calling, reasoning).
- **TTS (Text-to-Speech):** ElevenLabs, Cartesia.
- **Telephony:** Twilio, Plivo, Vapi, Retell AI.

### 2. Latency Optimization
- **Target:** < 800ms total turn-around time.
- **Understanding:** Knowing why 1.5s kills a call and how to optimize each leg of the STT -> LLM -> TTS pipeline.

### 3. Conversation & Agentic Design
- **Human Messiness:** Handling interruptions (barge-in), background noise, and accents.
- **Reasoning Loops:** Building agents that can "Think" before they "Speak" and use tools (CRM/Calendar) in real-time.
- **Handoff:** Designing clean logic for when an agent should transfer a call to a human.

### 4. Technical Integration
- **Frameworks:** Python/Node.js.
- **Advanced:** LangGraph, CrewAI (for multi-agent logic).
- **Protocols:** MCP (Model Context Protocol) for connecting agents to external tools.

## Key Interview Themes to Prepare

1.  **Latency Deep Dive:** How do you shave off 200ms from an ElevenLabs TTS stream? (WebSocket streaming, chunking).
2.  **Handling Interruptions:** How do you implement "Barge-in" logic so the agent stops speaking immediately when the user interrupts?
3.  **Tool Use on the Fly:** Describe building an agent that checks a Google Calendar and books a slot *while* talking to the user.
4.  **Prompt Engineering for Voice:** How do you write prompts that result in "Natural" spoken rhythm rather than "Wiki-style" text? (Filler words, shorter sentences).
5.  **Production Reliability:** How do you handle a scenario where the LLM latency spikes during a call? (Fallback "Uh-huh" or "One moment" fillers).
6.  **Compliance:** Understanding TCPA (Telephone Consumer Protection Act) for outbound calling and HIPAA for medical use cases.

## Technical "Must-Haves" for the Interview
- Hands-on experience with **Vapi, Retell, or Twilio**.
- Deep understanding of **WebSockets** for real-time audio streaming.
- Proficiency in **Python/Node.js**.
- Proven track record of shipping **Voice Agents** to real users.
