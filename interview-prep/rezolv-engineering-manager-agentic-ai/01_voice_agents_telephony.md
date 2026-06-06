# Module 01: Voice Agents & Telephony Orchestration

Real-time voice agents represent the frontline of Rezolv's Lending OS. Designing system architecture for voice requires deep control over network streams, real-time audio chunking, and precise turn-taking logic.

---

## 1. Architectural Blueprint: Voice AI Loop
A real-time voice call is orchestrated as a circular pipeline. Minimizing latency across this pipeline is critical for human-like conversations:

```
                  +-----------------------------------+
                  |        Telephony / PSTN           |
                  | (Twilio / Exotel / Ozonetel SIP)  |
                  +-----------------+-----------------+
                                    |
                    Bi-directional  |  WebSocket Audio
                    Audio Stream    |  (8kHz / 16kHz PCM/mu-law)
                                    v
                  +-----------------------------------+
                  |       Media Orchestrator          |
                  |   (FastAPI/Go/Node.js Server)     |
                  +-----+-----------------------^-----+
                        |                       |
       Raw Audio chunks |                       | Chunked Audio
       (e.g., 20-50ms)  v                       | Output
                  +-----+-------+        +------+-----+
                  |   ASR/STT   |        |   TTS      |
                  | (Deepgram)  |        | (ElevenLabs|
                  +-----+-------+        | /Sarvam)   |
                        |                +------^-----+
                        |                       |
         Text (Interim/ |                       | Text chunks
         Final)         v                       | (Words/Sentences)
                  +-----+-----------------------+-----+
                  |             LLM Brain             |
                  | (Haiku / GPT-4o-mini / Groq LLMs)  |
                  +-----------------------------------+
```

---

## 2. Key Telephony & Media Stream Technologies
*   **SIP & RTP (Session Initiation Protocol & Real-time Transport Protocol):** The backbone of PSTN routing. Orchestrators receive incoming calls from SIP trunks via platforms like Twilio Elastic SIP Trunking or Exotel/Ozonetel's SIP integrations.
*   **WebSockets vs. WebRTC:** WebSockets are standard for server-to-telephony integration (e.g., Twilio Media Streams send μ-law audio over WebSockets). WebRTC is preferred for browser-to-browser or mobile-app calling because of built-in jitter buffers, AEC (Acoustic Echo Cancellation), and NAT traversal.
*   **Audio Encodings:**
    *   **PCMU / G.711 μ-law:** 8kHz sampling rate, 8-bit mono. The default telephony standard (64 kbps). Low quality but highly robust.
    *   **PCM Raw:** Uncompressed audio, usually 16kHz, 16-bit mono. Better for ASR models but requires higher bandwidth.

---

## 3. High-Fidelity Turn-Taking Mechanics
Handling conversational state when two people talk is the hardest part of voice engineering.

### Voice Activity Detection (VAD)
VAD determines when the user is speaking. 
*   **Silero VAD / WebRTC VAD:** Lightweight ML models run locally on the orchestrator server to flag audio chunks containing speech vs. noise.
*   **Inactivity Threshold (Endpointing):** The delay wait-time before the agent decides the user has finished their turn.
    *   *Collections/Reminders:* Set short (400-600ms) for fast validation (e.g., "Yes, I paid").
    *   *Complex Explanation:* Set longer (800-1200ms) to prevent interrupting users who are thinking or pausing mid-sentence.

### Barge-In & Buffer Management
*   **Barge-in Detection:** If the user starts speaking while the agent is speaking, the VAD detects speech, and the orchestrator must **interrupt** the agent.
*   **Interrupt Sequence:**
    1.  Send a `clear` or `stop` command to the telephony platform (e.g., Twilio SIP instruction or media stream control) to stop playback immediately.
    2.  Clear the LLM output generation queue.
    3.  Clear the TTS audio buffer queue.
    4.  Update conversational state: context must reflect that the user interrupted the agent, tracking what part of the prompt was actually spoken before the interruption.

---

## 4. Target Interview Questions & High-Score Answers

### Q1: Walk me through the state machine of an active voice agent call. How do you handle packet loss or jitter over WebSockets?
**Answer:**
An active call is modeled using a Finite State Machine (FSM) containing states: `Listening`, `Processing` (waiting on LLM/TTS), `Speaking`, and `Interrupted`. 
WebSockets run over TCP, which guarantees packet delivery but can cause head-of-line blocking and variable latency (jitter) under poor network conditions. To handle this:
1.  **Jitter Buffer:** We implement an audio playback buffer on the client/SIP gateway side that caches a small window (e.g., 60-120ms) of incoming TTS PCM chunks to ensure smooth audio playback even if packets arrive out of order.
2.  **Keep-Alives:** Send empty ping/pong frames every 5-10 seconds to keep firewall connections open and identify network drops.
3.  **Fallback to SIP:** If WebSocket connectivity drops for more than 2 seconds, we fall back to playing standard telephonic DTMF (keypress) menus or standard IVR audio files stored on a CDN, preventing silence on the line.

### Q2: How do you configure Twilio Media Streams or Exotel Webhooks to stream audio bi-directionally? What is the payload structure?
**Answer:**
For Twilio, we use TwiML with the `<Connect>` and `<Stream>` verb pointing to our orchestrator's secure WebSocket endpoint (`wss://`).
Twilio streams 8kHz μ-law audio formatted as base64-encoded strings inside JSON messages. The lifecycle messages include:
*   `start`: Contains metadata (call SID, stream SID, audio format).
*   `media`: The actual stream payload containing the base64-encoded audio chunk (typically 20ms of audio, ~160 bytes).
*   `stop`: Sent when the stream completes.
To send audio back to the caller, we send a JSON object with a `media` type, containing the base64-encoded audio chunk and the active `streamSid`.

### Q3: When the user interrupts (barge-in), what exact steps does your backend take? How do you prevent the LLM from responding to its own voice or partial words?
**Answer:**
When VAD flags user speech during playback:
1.  **Immediate Mute:** We send a `Clear` command payload over the WebSocket to Twilio to flush the audio output buffer on the telephony side.
2.  **Process Interruption:** We kill the active TTS generation process and stop the LLM streaming thread.
3.  **Context Alignment:** We calculate how much of the TTS audio was actually played to the user (based on timestamps of chunks sent vs. played ack messages). We append only the *actually heard* portion to the LLM system prompt memory.
4.  **Acoustic Echo Cancellation (AEC):** The telephony provider or SIP bridge must have AEC active. Without AEC, the user's microphone picks up the agent's voice, triggering a false VAD flag. We verify AEC configurations at the SIP trunk level.

### Q4: In debt collections, voice agents must handle regional Indian dialects and code-switching (e.g., Hindi + English = Hinglish). How does this affect your VAD and ASR strategy?
**Answer:**
Dialect-heavy and code-switching environments impact VAD and ASR significantly:
1.  **VAD Robustness:** Standard VADs trained on English datasets sometimes fail on Indian accents, either cutting off vowels early or misclassifying noise. We use Silero VAD tuned on multilingual sets and set a dynamic inactivity timeout (e.g., 700ms default, shrinking to 400ms for confirmation states).
2.  **Multilingual ASR:** We use ASR models optimized for Indian code-switching, such as Sarvam's speech APIs or Deepgram's `nova-2-general` with `hi-IN` (Hindi) or customized regional language configurations.
3.  **Dialect Fallbacks:** If the ASR confidence falls below 0.65, our system defaults to asking the user to press a keypad (DTMF) button (e.g., "Press 1 to confirm payment delay") or routes the call to a human collector.
