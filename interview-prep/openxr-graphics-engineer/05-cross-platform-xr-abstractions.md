# 05 - Cross-Platform XR Abstractions

## Abstraction Layers

### 1. How do you handle "Graphics API" abstraction?
**Answer:**
Building a wrapper or using a cross-platform graphics library (like **bgfx** or **Vulkan**) that allows the same rendering code to run on Windows (DirectX/Vulkan) and Android (Vulkan/OpenGL ES).

### 2. Difference between Quest (Android) and SteamVR (Windows) build pipelines?
**Answer:**
- **Quest:** Requires ARM64 cross-compilation, handling Android permissions, and optimizing for mobile thermal limits.
- **SteamVR:** X64 architecture, standard Windows APIs, and typically higher power/thermal limits.

### 3. How do you handle "Input" differences across headsets?
**Answer:**
Using OpenXR **Interaction Profiles**. You define your actions and then provide suggested bindings for each profile (e.g., `khr_simple_controller`, `oculus_touch_controller`, `valve_index_controller`).

## Testing & Stability

### 4. How do you test XR applications without a headset?
**Answer:**
- **Simulation:** Using a "Mock Runtime" or the **OpenXR API Layers** to simulate tracking data and inputs.
- **2D Mode:** Developing a "Flat" mode where the camera is controlled by mouse/keyboard.

### 5. What is the "OpenXR Loader"?
**Answer:**
A library that applications link against. It discovers and loads the appropriate XR runtime (e.g., Oculus, SteamVR, WMR) installed on the user's system.

### 6. Importance of "Automated UI Testing" in XR?
**Answer:**
Difficult but necessary. Often involves recording input sequences and comparing rendered screenshots or checking the final state of the game world.
