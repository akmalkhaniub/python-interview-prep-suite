# 05 - Mobile & React Native Deep Dive

## Mobile-Specific Engineering

### 1. How do you handle "Navigation" in React Native?
**Answer:**
Using **React Navigation**.
- **Stack Navigation:** For moving between screens (Push/Pop).
- **Tab Navigation:** For bottom/top navigation bars.
- **Drawer Navigation:** For side menus.
- **Deep Linking:** Allowing external URLs to open specific screens in the app.

### 2. Difference between `FlatList` and `ScrollView`?
**Answer:**
- **ScrollView:** Renders all items at once. Fine for small lists.
- **FlatList:** Uses "Lazy Loading." It only renders the items currently visible on the screen. **Crucial for performance** with large datasets.

### 3. How do you handle "Native Modules"?
**Answer:**
If a feature isn't available in the standard React Native API (e.g., a specific hardware sensor), you write native code (Java/Swift) and expose it to JavaScript using a "Native Module" or "TurboModule."

## Performance & Interaction

### 4. What is the "React Native Reanimated" library?
**Answer:**
A powerful library for creating smooth animations that run on the **UI thread** rather than the JS thread, preventing stuttering during heavy computations.

### 5. How do you handle "Gestures"?
**Answer:**
Using **React Native Gesture Handler**. It provides native-driven gesture recognition for swipes, pinches, and rotations, which is much more performant than the built-in Responder system.

### 6. Challenges of "Cross-Platform" UI?
**Answer:**
- **Keyboard Handling:** Android and iOS handle keyboards differently (use `KeyboardAvoidingView`).
- **Platform Defaults:** Buttons and inputs look different by default.
- **Safe Areas:** Handling notches and home indicators (`SafeAreaView`).
- **Permissions:** Requesting camera/location permissions differs across OS versions.
