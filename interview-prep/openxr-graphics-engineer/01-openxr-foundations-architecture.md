# 01 - OpenXR Foundations & Architecture

## OpenXR Spec Basics

### 1. What is OpenXR and why is it important?
**Answer:**
An open, royalty-free standard for access to virtual reality (VR) and augmented reality (AR) platforms and devices.
- **Importance:** It allows developers to target a wide range of hardware (Quest, Valve Index, Varjo, etc.) with a single codebase, eliminating the need to implement vendor-specific SDKs (Oculus SDK, OpenVR SDK).

### 2. Explain the OpenXR "Handle" Hierarchy.
**Answer:**
- **XrInstance:** The connection to the OpenXR runtime.
- **XrSystemId:** Represents a hardware system (e.g., a specific headset).
- **XrSession:** Represents the application's lifecycle in the XR environment (ready, running, stopping).
- **XrSpace:** Represents a coordinate system (Stage, View, Local).

### 3. What is an `XrSwapchain`?
**Answer:**
A set of textures (image buffers) that the application renders into. The runtime takes these images and displays them on the headset. It is the core link between the Graphics API (Vulkan/DX) and the XR Runtime.

## Runtime Lifecycle

### 4. Explain the `xrWaitFrame`, `xrBeginFrame`, `xrEndFrame` loop.
**Answer:**
- **xrWaitFrame:** Blocks until the runtime is ready for the app to start calculating the next frame. Returns the predicted time when the frame will be visible.
- **xrBeginFrame:** Marks the start of frame execution.
- **xrEndFrame:** Submits the rendered layers (swapchains) to the runtime for composition and display.

### 5. What are "Layers" (XrCompositionLayer) in OpenXR?
**Answer:**
The runtime can composite multiple layers (Projection, Quad, Cylinder) together.
- **Projection:** Standard 3D view.
- **Quad:** Used for UI overlays or 2D content.
- **Benefit:** High-quality text/UI that is composited separately from the 3D world, often at a higher resolution or with better sampling.

### 6. Difference between "Headless" and "Graphics-enabled" OpenXR?
**Answer:**
- **Graphics-enabled:** The standard mode where the app renders 3D views.
- **Headless:** Used for tracking-only applications or background services that don't need to render to the display.
