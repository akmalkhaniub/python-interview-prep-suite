# 7-Day Interview Preparation Plan: Senior AI Engineer (Voice Focus)

This intensive plan is designed to prepare you for the Hubaix interview, focusing specifically on real-time voice pipelines, LLM integration, and multi-tenant SaaS architecture.

## Day 1: Real-Time Audio & Telephony Protocols
- **Action:** Review WebSockets, WebRTC, and SIP/telephony fundamentals.
- **Focus:** Understand how audio is streamed, chunked, and handled over websockets. Brush up on sampling rates (8kHz for telephony vs. 16kHz+ for modern STT).
- **Output:** Be able to draw a network diagram showing how audio flows from a browser/phone to an STT engine in real-time.

## Day 2: STT (Speech-to-Text) & TTS (Text-to-Speech) Deep Dive
- **Action:** Compare API vs. Self-hosted options.
- **Focus:** 
  - APIs: Deepgram (streaming), ElevenLabs (low latency WebSockets).
  - Open Source: Whisper (WhisperX for diarization/batch vs. streaming Whisper), Coqui TTS, VITS.
- **Output:** Prepare technical talking points on *why* you would choose a specific STT/TTS stack for a low-latency requirement vs. an offline/HIPAA requirement.

## Day 3: LLM Streaming & Agentic Architectures (LangGraph)
- **Action:** Review LangChain, LangGraph, and streaming token generation.
- **Focus:** How to yield tokens as they are generated, how to pass those tokens to a TTS engine, and how LangGraph manages conversational state and tool use.
- **Output:** Build (or mentally map out) a simple Python script that connects an LLM streaming response directly to a streaming TTS API.

## Day 4: Handling Interruption & Latency Optimization (The "Voice AI Holy Grail")
- **Action:** Study Voice Activity Detection (VAD) and barge-in mechanics.
- **Focus:** How do you detect an interruption (Silero VAD)? How do you kill the active LLM/TTS threads safely? How do you update the context window so the LLM knows *where* it was interrupted?
- **Output:** Draft a 3-step technical explanation on how you handle user interruptions in a voice bot.

## Day 5: Multi-Tenant SaaS Architecture
- **Action:** Review cloud architecture for SaaS.
- **Focus:** Data isolation (Postgres RLS), dynamic model loading (LoRAX/vLLM for serving multiple LoRA adapters), Redis for session state, and API routing.
- **Output:** Be able to explain how you can support 100 different businesses on your platform without their custom prompts/data leaking into each other.

## Day 6: Compliance & Offline Deployments
- **Action:** Review HIPAA and GDPR principles in the context of AI.
- **Focus:** Private VPC deployments, PII redaction before LLM processing, and local GPU deployment using vLLM or Ollama. 
- **Output:** Prepare a 5-minute explanation on how you would migrate a cloud-API dependent app (OpenAI + Deepgram) to a fully self-hosted, HIPAA-compliant on-premise stack.

## Day 7: Mock Interview & Storytelling
- **Action:** Practice your technical narrative.
- **Focus:** Use the STAR method to describe:
  1. A time you reduced latency in an AI system.
  2. A time you integrated an LLM into a production product.
  3. A complex debugging issue involving audio or streaming.
- **Output:** Polish your resume walkthrough, heavily emphasizing "production LLM experience" and "voice bot architecture."
