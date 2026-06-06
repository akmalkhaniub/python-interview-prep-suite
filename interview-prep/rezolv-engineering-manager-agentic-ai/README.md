# Interview Prep: Engineering Manager (Agentic AI Platform) - Rezolv

This directory contains preparation materials and deep-dives tailored for the **Engineering Manager - Agentic AI Platform** role at **Rezolv**. The position is a high-impact player-coach role requiring mastery of voice orchestration, speech intelligence, multimodal data extraction, enterprise SaaS scaling, and high-caliber engineering leadership in the regulated Indian financial sector (BFSI).

---

## 📂 Preparation Roadmap

### [01. Voice Agents & Telephony Orchestration](./01_voice_agents_telephony.md)
*   Full-lifecycle conversational voice loops (reminders, collections, recovery, verification calls).
*   Low-latency turn-taking, barge-in detection, VAD calibration, and buffer-clearing mechanisms.
*   Telephony integration using WebSockets with Twilio, Exotel, Ozonetel, etc.

### [02. Voice Analytics & Signal Extraction](./02_voice_analytics_signal_extraction.md)
*   ASR/STT & TTS engines (Deepgram, Sarvam, ElevenLabs, Azure Speech).
*   Actionable signal extraction: audio transcription, translation, and tone/sentiment/intent classification.
*   Handling noise, ambient audio, compression artifacts, and scale.

### [03. Multimodal Document Processing](./03_multimodal_document_processing.md)
*   Structured data extraction from financial documents (PDFs, images, bank statements, video/audio recordings).
*   Multimodal LLMs (GPT-4o, Gemini Flash/Pro, Claude Sonnet) and hybrid OCR-LLM architectures.
*   Parsing unstructured financial data and verification/comparison workflows in lending.

### [04. Agentic Workflows, Evaluation & Observability](./04_agentic_workflows_evaluation_observability.md)
*   Designing agentic loops (ReAct, Planning, Tool Use) and RAG architectures for lending constraints.
*   Production evaluation suites (Ragas, TruLens) for prompt safety, hallucinations, and alignment.
*   Observability frameworks (LangSmith, Arize Phoenix, OpenTelemetry) and runtime guardrails.

### [05. Platform Reliability, Latency & Economics](./05_platform_reliability_latency_economics.md)
*   Optimizing end-to-end voice latency to sub-800ms.
*   Multi-tenant SaaS architectures in banking, data isolation, and strict regulatory compliance (RBI guardrails).
*   Cost control, rate limiting, token caching, and unit economics of running LLMs at scale.

### [06. Team Leadership & Enterprise Delivery](./06_team_leadership_enterprise_delivery.md)
*   Managing player-coach balance (code contribution vs. delegation/architecture).
*   Scaling high-performing teams of AI and product engineers.
*   Managing stakeholders (founders, product managers, bank deployment teams, and bank CXOs).

---

## 🚀 Key Architectural Themes for Rezolv
1.  **Low Latency is King:** Voice calls cannot tolerate >800ms response delays. Every stage of the pipeline (VAD -> ASR -> LLM -> TTS -> Playback) must be optimized.
2.  **RBI Compliance & Security:** Financial data in India is strictly regulated. All AI pipelines must support on-prem, private VPC, or localized SaaS models with air-tight tenant data isolation.
3.  **Lending Context Mastery:** The collection calls are high-stress and highly sensitive. Voice agents must be deterministic, polite, legally compliant, and robust to local dialects/mixes (Hinglish, Kanglish, etc.).
