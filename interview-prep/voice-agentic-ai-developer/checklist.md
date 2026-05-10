# 7-Day Interview Prep Checklist: Voice Agentic AI Developer

## Day 1: The Voice AI Stack
- [ ] Review **Deepgram & AssemblyAI** WebSocket APIs for streaming STT.
- [ ] Practice building a simple **ElevenLabs** streaming TTS script in Python/Node.js.
- [ ] Research **Cartesia** and why it's gaining traction for low-latency voice.

## Day 2: Telephony & Orchestration (Vapi/Twilio)
- [ ] Review **Vapi or Retell AI** documentation for inbound/outbound calling.
- [ ] Practice setting up a **Twilio Media Stream** and handling raw audio bytes.
- [ ] Study the **"Barge-in"** pattern: How to stop audio playback on user speech.

## Day 3: Latency Optimization Techniques
- [ ] Review **WebSockets vs. REST** for real-time applications.
- [ ] Practice implementing **Natural Fillers** (e.g., "Mmm," "I see") to hide LLM latency.
- [ ] Study **TTFT (Time to First Token)** optimization for Claude/GPT-4o.

## Day 4: Agentic Reasoning & Tool Use
- [ ] Review **LangGraph** for building stateful voice agents.
- [ ] Practice a **Tool Calling** scenario: Check a Mock Calendar while in a "Call."
- [ ] Study **MCP (Model Context Protocol)** for standardizing tool integration.

## Day 5: Conversation Design & Messiness
- [ ] Review **Prompt Engineering** for spoken language (prosody, filler words).
- [ ] Practice designing a **State Machine** that handles interruptions and off-topic questions.
- [ ] Study **Handoff Logic**: When to transfer to a human representative.

## Day 6: Compliance & Production Monitoring
- [ ] Review **TCPA** regulations for automated outbound calling.
- [ ] Study **HIPAA** requirements for handling healthcare audio data.
- [ ] Practice designing a **Monitoring Dashboard** for Call Success Rates and Latency.

## Day 7: Pitch & Demo Prep
- [ ] Finalize your **"Voice Agent Success Stories"**: Calls handled, latency achieved.
- [ ] Prepare your "Latency First" pitch: How you get under 800ms.
- [ ] Mock interview: Explain the **Audio-Buffer Flushing** logic needed for interruptions.
