# Deep Dive: Voice AI Stack & Latency Optimization

In voice AI, latency is the ultimate killer of immersion. A delay of 1.5s feels like a walkie-talkie; <800ms feels like a human.

## 1. The Anatomy of a Voice Turn
To optimize latency, you must measure and shave time from each component:
1.  **VAD (Voice Activity Detection):** Detecting when the user stopped talking (Target: 100-200ms).
2.  **STT (Speech-to-Text):** Converting audio to text (Target: 100-300ms).
3.  **LLM (Time to First Token):** Generating the start of the response (Target: 200-400ms).
4.  **TTS (Time to First Audio):** Converting the first chunk of text to audio (Target: 100-200ms).

## 2. Low-Latency STT (Deepgram)
*   **WebSockets:** Always use streaming WebSockets instead of REST API.
*   **Interim Results:** Using interim transcripts to "anticipate" the user's intent.
*   **Endpointing:** Tuning the "Inactivity duration" to distinguish between a "breath" and the "end of a sentence."

## 3. Optimizing the LLM Brain
*   **Context Pruning:** Keeping the prompt concise to reduce processing time.
*   **Small vs. Large Models:** Using faster models (GPT-4o mini, Claude Haiku) for quick conversational turns, and routing to larger models for complex reasoning.
*   **Speculative Generation:** Starting the TTS stream before the LLM has finished the entire sentence.

## 4. Real-Time TTS (ElevenLabs/Cartesia)
*   **Streaming API:** Using the `text-to-speech-stream` endpoint.
*   **Chunking:** Sending text to the TTS engine as soon as the first few tokens are available from the LLM.
*   **Optimization:** Choosing the "Turbo" models which are specifically designed for low-latency voice applications.

## 5. Telephony Integration (Twilio/Vapi)
*   **WebSocket Audio:** Pushing the generated TTS audio directly into the Twilio Media Stream.
*   **Barge-in:** When the telephony layer detects incoming audio from the user, it must immediately send a "Clear Buffer" signal to the TTS and LLM engines to stop the current response.

## Interview Questions
1.  "Walk me through the full lifecycle of a voice turn. Where are the biggest latency bottlenecks?"
2.  "How do you implement 'Natural Fillers' (e.g., 'Let me see...') to buy time for a complex LLM query?"
3.  "Describe your experience with WebSockets for real-time audio. How do you handle packet loss or jitter?"
4.  "If you had to get a system from 1.2s down to 700ms, which component would you optimize first and why?"
