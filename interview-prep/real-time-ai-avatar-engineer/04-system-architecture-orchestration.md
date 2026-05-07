# Deep Dive: System Architecture & Orchestration

## 🏗️ The High-Level Architecture
The system is divided into **Client (Next.js)**, **Stream Processor (Node/Python)**, and **Media Server (WebRTC SFU)**.

### 1. Frontend (Next.js 14)
*   **State Management:** React Context or Zustand to manage "Avatar Mode" vs "Webcam Mode".
*   **Asset Loading:** Pre-fetching GLB models and textures to avoid loading lag during a meeting.
*   **UI/UX:** Overlaying the Avatar preview on top of the meeting controls.

### 2. Backend Orchestration
*   **Session Management:** Redis for tracking active avatar sessions and signaling states.
*   **Stream Routing:** A Node.js server acting as a controller for the SFU (e.g., LiveKit).
*   **Worker Nodes:** If rendering is server-side (Headless Chrome/Unity), these nodes pull audio via WebRTC, render, and push video back.

## 📦 Scalability Strategy
*   **GPU Scheduling:** If using server-side rendering, use **Kubernetes with NVIDIA Device Plugin** to share GPUs across multiple avatar instances.
*   **Edge Deployment:** Deploy the Media Server (SFU) close to the user (AWS Wavelength or Cloudflare Calls) to minimize latency.

## 🔒 Security & Privacy (JD Requirement)
*   **No Raw Storage:** Raw webcam feed is processed locally (client-side) to generate animation data. Only the **animation coefficients** (e.g., blendshape values) are sent to the server, never the raw video.
*   **E2EE:** Use WebRTC Insertable Streams for end-to-end encryption if the media must pass through a server.

## 💡 Interview Q&A
**Q: How do you handle 1000+ concurrent avatar sessions?**
**A:** Distributed SFU mesh. Use an SFU like **Mediasoup** or **LiveKit** which supports multi-node scaling. Offload as much processing as possible to the **client-side (WebGL)** to minimize server costs.

**Q: Describe the "Avatar Lifecycle" API.**
**A:**
1.  `POST /session/init`: Authenticate user and fetch avatar profile.
2.  `POST /session/signaling`: Exchange WebRTC offer/answer.
3.  `GET /assets/{avatar_id}`: Stream encrypted GLB model data.
4.  `DELETE /session/{id}`: Clean up WebRTC peer connections and Redis state.

## 📊 Performance Indicators (KPIs)
*   **M2M (Mouth-to-Mouth) Latency:** < 150ms.
*   **CPU Usage:** < 15% on average consumer laptop.
*   **Sync Accuracy:** Lip movement should match audio within 2 frames (66ms).
