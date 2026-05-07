# Deep Dive: Real-Time Streaming & WebRTC

## 📡 Why WebRTC?
For the AI Avatar system, latency is the #1 enemy. WebRTC is the industry standard for sub-second latency because:
*   **UDP-based:** Prioritizes timeliness over reliability (better to drop a frame than to wait for a retransmission).
*   **P2P (mostly):** Minimizes hops, though we often use an SFU (Selective Forwarding Unit) for scalability.

## 🏗️ The WebRTC Pipeline for Avatars

### 1. Capture & Signaling
*   **Audio:** Captured via `getUserMedia()` and sent to Deepgram for transcription AND the animation engine.
*   **Signaling:** Using WebSockets or HTTP/gRPC to exchange SDP (Session Description Protocol) and ICE Candidates.

### 2. SFU vs MCU Architecture
*   **SFU (Selective Forwarding Unit):** Forwards media streams to participants without transcoding. Minimal CPU overhead. (e.g., LiveKit, Mediasoup).
*   **MCU (Multipoint Control Unit):** Mixes multiple streams into one. High CPU, but saves client bandwidth. **Not ideal for low-latency avatars.**

## ⚡ Latency Optimization Strategies
*   **Jitter Buffer Tuning:** Keep the buffer small (e.g., 50-100ms) to reduce delay, at the cost of potential stutter.
*   **Opus Audio Codec:** Use low-latency Opus settings (CBR, 20ms frames).
*   **Local Prediction:** Predict the next viseme based on the current audio chunk before the full transcription is ready.

## 💡 Interview Q&A
**Q: How do you handle network congestion in a WebRTC-based avatar stream?**
**A:** Implement **ABR (Adaptive Bitrate Control)** using Google Congestion Control (GCC). Monitor RTCP reports (Round Trip Time, Packet Loss) and dynamically scale down the avatar rendering quality (e.g., reduce texture size or FPS) to maintain smooth motion.

**Q: WebRTC typically uses DTLS/SRTP. How does this impact performance?**
**A:** Encryption is mandatory in WebRTC. Modern CPUs have hardware acceleration (AES-NI) for SRTP, so the overhead is negligible (~1-2ms), but it's critical for the "Security & Privacy" requirement of the JD.

## 🛠️ Code Snippet: Basic WebRTC Audio Data Channel
```javascript
// Capturing audio to send to an animation worker
const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
const audioContext = new AudioContext();
const source = audioContext.createMediaStreamSource(stream);
const processor = audioContext.createScriptProcessor(4096, 1, 1);

processor.onaudioprocess = (e) => {
  const inputData = e.inputBuffer.getChannelData(0);
  // Send raw PCM data to the Avatar Engine via WebRTC DataChannel for low-latency sync
  dataChannel.send(inputData); 
};
```
