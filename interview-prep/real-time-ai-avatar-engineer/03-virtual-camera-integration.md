# Deep Dive: Virtual Camera & Meeting Integration

## 🎥 The Problem: Meeting Platform Compatibility
Zoom, Google Meet, and Teams expect a system-level webcam. A web app (Next.js) doesn't have direct access to "become" a hardware camera.

## 🛠️ Implementation Approaches

### 1. Browser-to-Browser (WebRTC Injection)
*   **How it works:** A browser extension replaces the `navigator.mediaDevices.getUserMedia` function with a custom stream (the avatar canvas).
*   **Pros:** Works instantly in Google Meet/Teams (web versions). No installation required.
*   **Cons:** Doesn't work for Desktop apps.

### 2. Virtual Camera Driver (Desktop)
*   **OBS Virtual Camera:** The most robust way. We can programmatically control OBS via **obs-websocket**.
*   **Custom Driver:** Using **v4l2loopback** (Linux) or **DirectShow Filters** (Windows).
*   **Logic Flow:**
    1.  Next.js app renders the Avatar in a hidden Electron window or Headless Browser.
    2.  The output is piped to the Virtual Camera Driver.
    3.  Zoom sees "Viva Avatar Cam" as a selectable input.

### 3. WebRTC-to-RTMP/VCD Bridge
*   Next.js app streams the avatar via WebRTC to a Node.js/Python server.
*   The server uses **FFmpeg** to convert the WebRTC stream to a virtual device.
    ```bash
    ffmpeg -i rtmp://localhost/live/avatar -f v4l2 /dev/video0
    ```

## 🔗 Integration with Meeting APIs
*   **Zoom SDK:** Allows custom video overlays and raw video access.
*   **Microsoft Teams Apps:** Can use the "Stage View" or "Side Panel" to show the avatar controls.

## 💡 Interview Q&A
**Q: How would you ensure the avatar works on both Mac and Windows?**
**A:** Use **Electron** for a desktop wrapper. Electron allows us to bundle native virtual camera binaries or interact with system-level APIs that a standard browser cannot touch.

**Q: If a user has a slow GPU, the virtual camera might lag. How do you mitigate this?**
**A:** Fallback mechanisms. If the frame rate drops below 15 FPS, switch to a "Lite" version of the avatar (e.g., 2D static images with basic lip movement) or reduce the rendering resolution. Use `requestVideoFrameCallback` to monitor performance.

## 🛠️ Code Snippet: Canvas to Stream (Injection Pattern)
```javascript
// Capturing the Three.js canvas as a media stream
const canvas = document.querySelector('canvas');
const avatarStream = canvas.captureStream(30); // 30 FPS

// Overriding the browser's webcam getter
const originalGetUserMedia = navigator.mediaDevices.getUserMedia;
navigator.mediaDevices.getUserMedia = async (constraints) => {
  if (constraints.video && !constraints.audio) {
    return avatarStream; // Return the avatar instead of the webcam
  }
  return originalGetUserMedia.call(navigator.mediaDevices, constraints);
};
```
