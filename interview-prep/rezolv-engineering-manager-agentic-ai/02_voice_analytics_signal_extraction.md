# Module 02: Voice Analytics & Signal Extraction

At Rezolv, voice analytics transforms unstructured audio streams from collections and customer service calls into structured, actionable business intelligence (compliance tracking, sentiment, payment commitments).

---

## 1. High-Performance ASR & TTS Selection Matrix
Choosing the right model dictates voice quality, language capability, and server latency:

| Engine | Primary Strength | Weakness | Use Case in Lending |
|---|---|---|---|
| **Deepgram (Nova-2)** | Extreme speed, highly accurate for English and Spanish, competitive pricing. | Weak on regional Indian dialects and deep code-switching. | Standard English ASR, general verification calls. |
| **Sarvam AI (Shikhara)** | Native support for regional Indian languages (Hindi, Tamil, Telugu, etc.), excellent code-switching (Hinglish). | Higher latency than Deepgram. | Vernacular debt collections, tier-2/3 Indian city campaigns. |
| **ElevenLabs** | Ultra-realistic, emotional, natural prosody (pitch & rhythm). | Expensive, higher latency. | High-value customer welcome calling, premium customer service. |
| **Azure Speech** | Standard, reliable, enterprise SLA, hybrid/on-prem options. | Lacks conversational realism of ElevenLabs. | Scaled collections voice synthesis, high-volume transactional calls. |

---

## 2. Low-Bitrate Telephony Challenges (8kHz PCMU)
Telephony networks utilize G.711 / μ-law compression at 8kHz sampling rate. Standard audio ML models are trained on 16kHz or 48kHz audio.
*   **Up-sampling:** Translating 8kHz to 16kHz using linear interpolation or ML up-sampling models before sending to ASR engines to prevent recognition drops.
*   **Noise Profiles:** Telephony audio contains high ambient noise, road noises, cellular network packet losses, and compression artifacts.
*   **Speech Enhancement:** Passing audio through real-time noise reduction filters (e.g., RNNNoise, Spectral Gating) to isolate vocals before processing.

---

## 3. Signal Extraction & Analytics Pipeline
Once the call is transcribed (or streaming), we run asynchronous pipelines to extract:
1.  **Intent Classification:** Identifying why the borrower is delinquent (e.g., medical emergency, bank server error, lack of funds).
2.  **Sentiment and Tone Analysis:** Measuring user volume, pitch variance, and language patterns to detect anger, frustration, or fraud signals.
3.  **Compliance Checks:** Ensuring the AI voice agent (or human agent) followed regulatory RBI guidelines (e.g., no threats, no calling outside hours, correct disclosure).

---

## 4. Target Interview Questions & High-Score Answers

### Q1: How do you build an asynchronous, highly-scalable translation and transcription pipeline for millions of call recordings?
**Answer:**
We architect this using an event-driven microservices model:
1.  **Ingestion:** Call recordings are pushed from the telephony server to an object store (e.g., AWS S3 or MinIO) which triggers an event on a message broker (RabbitMQ or Apache Kafka).
2.  **Workers:** A pool of Python worker services (orchestrated with Celery/Kubernetes) consumes these events.
3.  **ASR Chunking:** For long files, we run diarization (separating borrower and agent channels) and send chunked audio streams to Deepgram or Sarvam API endpoints in parallel.
4.  **Translation & LLM Summary:** The transcribed texts are routed to a translation layer if necessary, then to a fast LLM (such as Llama-3-70B or GPT-4o mini) to extract structured metrics (repayment date, compliance flags, sentiment).
5.  **Data Lake:** The final JSON payload is indexed in Elasticsearch for fast search/analytics and stored in PostgreSQL for reporting.

### Q2: What is the impact of G.711 / μ-law audio format on voice biometrics and sentiment analysis? How do you mitigate this?
**Answer:**
G.711 restricts the frequency range to 300Hz - 3.4kHz, throwing away high-frequency vocal details. This severely degrades standard acoustic sentiment models that look for subtle vocal jitter and micro-shakes.
To mitigate this:
1.  **Text-Acoustic Hybrid models:** We do not rely solely on acoustic signals for sentiment. We combine acoustic feature extractors (pitch, energy variance) with LLM semantic analysis of the transcript.
2.  **Model Calibration:** We train/fine-tune our vocal emotion classifier models directly on down-sampled 8kHz G.711 datasets so they learn acoustic features specific to phone call lines rather than studio quality audio.

### Q3: When translating local Indian dialects to English for bank dashboards, how do you handle idioms or expressions (e.g., "Paise kal milenge" or "Bank me server down tha")?
**Answer:**
Direct literal translation (e.g., Google Translate) often misses context or yields confusing results (e.g., translating "Mera server down hai" to "My server is low"). We implement a two-step approach:
1.  **Vernacular ASR:** Transcribe directly into native script (e.g., Devanagari script for Hindi).
2.  **Context-Aware LLM Translation:** Instead of standard translation APIs, we use LLMs with few-shot examples that map regional collection excuses to their financial meanings.
    *   *Input:* "Bank me server down tha" (Hinglish transcription)
    *   *System Prompt:* "Translate the collection dialogue to English. Preserve the legal and financial context of the borrower's payment intent."
    *   *Output:* "Payment failed due to bank server downtime."

### Q4: How do you optimize ElevenLabs synthesis cost and latency for dynamic variables (e.g., reading a specific borrower name and outstanding amount)?
**Answer:**
ElevenLabs is expensive and can introduce latency if synthesis is done on-the-fly. We optimize this using **hybrid caching**:
1.  **Pre-synthesis:** Standard scripts ("Hello, I am calling from ICICI Bank regarding your outstanding loan...") are pre-synthesized, stored as MP3 files, and cached.
2.  **Dynamic Stitching:** We synthesize only the dynamic variable (e.g., "Mr. Raj Kumar") and stitch it together with the pre-synthesized audio at runtime.
3.  **WebSocket Streaming:** For fully dynamic dialogues, we stream text chunks to ElevenLabs WebSocket API, which returns audio chunks back in real-time, allowing playback to begin as soon as the first word is synthesized.
