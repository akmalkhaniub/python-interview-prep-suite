# 01 - Frontend & React Native

## React Mastery

### 1. What are "Higher-Order Components" (HOCs)?
**Answer:**
A pattern in React where a function takes a component and returns a new component with added functionality. 
- **Use Case:** Code reuse, logic sharing (e.g., `withAuth`, `withLoading`).
- **Trend:** Mostly replaced by **Hooks** in modern React, but still important for legacy code and certain library patterns.

### 2. How do you handle Global State without a library?
**Answer:**
Using the **Context API** combined with `useReducer`. 
- **Context:** Passes data through the component tree without prop drilling.
- **useReducer:** Handles complex state transitions.
- **Tradeoff:** For very high-frequency updates, external libraries like Zustand or Redux are more performant.

### 3. Explain the React Lifecycle (Functional Components).
**Answer:**
Managed by `useEffect`:
- **Mounting:** `useEffect(() => { ... }, [])`.
- **Updating:** `useEffect(() => { ... }, [dependencies])`.
- **Unmounting:** `useEffect(() => { return () => { ... } }, [])`.

## React Native (Mobile)

### 4. What is the "Bridge" in React Native?
**Answer:**
The communication layer between JavaScript and the Native side (Objective-C/Swift for iOS, Java/Kotlin for Android). JS sends JSON-like messages to the Native side to trigger UI updates or access device hardware.
- **Note:** The new architecture (**TurboModules** / **Fabric**) is moving toward a direct JSI (JavaScript Interface) to eliminate the bridge bottleneck.

### 5. Difference between `View`, `Text`, and `ScrollView` in React Native vs. Web?
**Answer:**
- **View:** Similar to `<div>`.
- **Text:** Similar to `<span>` or `<p>`. In RN, all text must be wrapped in a `<Text>` component.
- **ScrollView:** Used for scrolling content. Unlike the web, containers don't scroll by default.

### 6. How do you handle different screen sizes in React Native?
**Answer:**
- **Flexbox:** RN uses a subset of Flexbox (default is `flexDirection: column`).
- **Dimensions API:** To get screen width/height.
- **PixelRatio:** To handle different pixel densities.
- **Libraries:** `react-native-responsive-screen` or `react-native-size-matters`.
