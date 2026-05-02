# 02 - XR Graphics & Rendering Pipelines

## Rendering for XR

### 1. What is "Multiview" (Single-Pass) rendering?
**Answer:**
A technique to render both eyes in a single draw call. The vertex shader broadcasts primitives to multiple views (layers) in the swapchain.
- **Benefit:** Significantly reduces the CPU overhead (fewer draw calls) and state changes compared to rendering eyes sequentially.

### 2. Explain "Fixed Foveated Rendering" (FFR).
**Answer:**
A technique where the resolution is reduced in the periphery of the display where the user isn't looking (or where the lens distortion makes high resolution wasted).
- **Dynamic Foveated Rendering (DFR):** Uses **Eye Tracking** to reduce resolution around where the user is looking.

### 3. How do you handle "Stereo Rendering" distortions?
**Answer:**
The runtime provides **Distortion Grids** or **Projection Matrices** tailored to the specific lenses of the headset. The app renders into a flat swapchain, and the runtime applies a final post-process to "barrel distort" the image to match the lens curvature.

## Shaders & APIs

### 4. Why is Vulkan favored for mobile XR (Quest)?
**Answer:**
Low-level access to hardware, multi-threaded command buffer submission, and explicit control over memory. It allows for better performance on mobile chipsets (Adreno) compared to OpenGL ES.

### 5. What are "Compute Shaders" used for in XR?
**Answer:**
- Physics calculations.
- Culling (Frustum/Occlusion).
- Post-processing effects (Blur, Bloom).
- Particle systems.

### 6. Explain "Alpha Blending" vs. "Additive" for AR (Passthrough).
**Answer:**
- **Passthrough (Quest):** Uses Alpha blending to composite digital content over the camera feed.
- **Optical See-Through (HoloLens):** Content is purely additive; you cannot render "black" (it appears transparent).
