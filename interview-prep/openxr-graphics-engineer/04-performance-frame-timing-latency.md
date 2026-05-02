# 04 - Performance, Frame Timing & Latency

## XR Latency

### 1. What is "Motion-to-Photon" Latency?
**Answer:**
The total time from the physical movement of the user's head to the light from the corresponding image hitting the user's eyes.
- **Target:** Should be **<20ms** to prevent motion sickness and ensure immersion.

### 2. Explain "Asynchronous Timewarp" (ATW).
**Answer:**
A runtime technique that takes the last rendered frame and "warps" it based on the very latest head tracking data just before the display refreshes.
- **Benefit:** If the app misses its frame deadline, ATW ensures the user still sees an updated image that matches their head rotation, preventing judder.

### 3. What is "SpaceWarp" (ASW)?
**Answer:**
A technique for generating "synthetic" frames. If the app is running at 45 FPS on a 90Hz display, ASW uses depth buffers and motion vectors to create the in-between frames.

## Performance Profiling

### 4. How do you profile XR applications?
**Answer:**
- **On-device:** Oculus Metrics Tool, Qualcomm Snapdragon Profiler.
- **GPU Profiling:** RenderDoc, Pix (DirectX).
- **CPU Profiling:** Tracing (Chrome Tracing, System Trace).
- **OpenXR:** Using the "Frame Timing" stats from the runtime.

### 5. Importance of "Draw Call Batching" for Mobile XR?
**Answer:**
Mobile CPUs (Quest) are easily bottlenecked by draw calls. Combining meshes with the same material (Static/Dynamic Batching) or using **Instancing** is critical to maintaining 72/90/120 FPS.

### 6. What is "Late Latching"?
**Answer:**
A technique to update constant buffers (like view/projection matrices) on the GPU at the very last possible moment before the draw call, using the absolute latest tracking data to reduce latency.
