# Deep Dive: Native Modules, JSI, & The New Architecture

As a Principal Engineer at Creative Chaos, you are expected to handle complex integrations that go beyond standard React components.

## 1. Traditional Native Modules (The Bridge)
*   **Asynchronous Nature:** Why the bridge is the primary bottleneck (JSON serialization/deserialization).
*   **Argument Mapping:** How types are mapped between JS and Native (ReadableMap, WritableArray).
*   **Threading:** The JS thread, the UI thread, and the Background (Native) thread.

## 2. The New Architecture (Fabric & TurboModules)
*   **JavaScript Interface (JSI):**
    - Eliminates the bridge.
    - Allows JS to hold references to C++ Host Objects.
    - Synchronous execution: Call a native function and get a result instantly (essential for things like animation or complex calculations).
*   **TurboModules:**
    - Strongly typed using **Codegen**.
    - Lazy loading (modules are only initialized when first called).
*   **Fabric Rendering:**
    - Concurrent rendering support.
    - Synchronous layout measurement (fixes the "jumpy" UI issues in complex transitions).

## 3. Practical Native Scenarios
### Scenario A: Custom Camera Filter
- Implementing a camera preview in Native (Swift/Kotlin) and exposing it as a React component using `requireNativeComponent` or Fabric's `NativeComponent`.
### Scenario B: Background Processing
- Using `WorkManager` (Android) or `BackgroundTasks` (iOS) to sync data when the app is killed or in the background, and communicating the status to the RN layer.

## 4. Performance Tuning
*   **Avoid unnecessary re-renders:** Use `React.memo`, `useCallback`, and `useMemo` strategically.
*   **JS Bundle Size:** Analyzing the bundle using `react-native-bundle-visualizer`.
*   **Native Memory Leaks:** Using Xcode Instruments (Leaks) and Android Studio Profiler to find native-side memory leaks caused by uncleaned event listeners.

## Interview Questions
1.  "Explain the difference between the 'Old Architecture' and 'The New Architecture' in React Native."
2.  "Describe a time you had to write custom Objective-C or Java code to solve a problem that a React Native library couldn't handle."
3.  "How do you ensure a smooth 60 FPS experience when rendering complex, data-heavy lists?"
4.  "What are the security implications of using a custom JSI module?"
