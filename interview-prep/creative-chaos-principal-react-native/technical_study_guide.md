# Technical Study Guide: Principal React Native Engineer (Creative Chaos)

## 1. The Native Bridge & Modern Architecture
- **JSI (JavaScript Interface):** How it allows direct synchronous calls between JS and Native.
- **TurboModules:** The next-gen native modules with lazy loading.
- **Fabric Rendering:** Understand how the new shadow tree and concurrent rendering work.
- **Codegen:** How to generate typed interfaces between TS and C++/Native code.

## 2. Advanced TypeScript & State Management
- **Generic Types:** Building reusable, type-safe components and hooks.
- **State Management:**
    - **Zustand/Redux:** Large-scale state patterns.
    - **React Query/Apollo:** Server-state and caching.
- **Immutability:** Importance of `immer` or structural sharing in mobile performance.

## 3. Native Development Skills (iOS/Android)
- **Swift/Objective-C:** Basic syntax for UIViews, ViewControllers, and Native Modules.
- **Kotlin/Java:** Activity lifecycle, Fragments, and Android Intent system.
- **CocoaPods & Gradle:** Managing native dependencies and build configurations.

## 4. Performance & Security
- **Hermes Engine:** Garbage collection and bytecode pre-compilation.
- **FlashList vs FlatList:** Optimizing long lists and memory usage.
- **Security:**
    - Keychain/Keystore for sensitive data.
    - Certificate Pinning.
    - Obfuscation (ProGuard, DexGuard).

## 5. GraphQL & Networking
- **Apollo Client:** Normalization, optimistic UI, and cache policies.
- **Error Handling:** Designing a resilient networking layer with interceptors.
- **Offline Storage:** SQLite, WatermelonDB, or MMKV (fastest KV store).

## 6. Leadership & Engineering Management
- **Code Review Culture:** Setting standards and providing constructive feedback.
- **System Design:** Designing a modular mobile architecture (Feature-based vs. Layer-based).
- **Hiring:** Technical interviewing techniques and evaluation rubrics.

## Resources to Review
- [React Native: The New Architecture](https://reactnative.dev/docs/the-new-architecture-intro)
- [Apollo Client: React Native Guide](https://www.apollographql.com/docs/react/integrations/react-native/)
- [FlashList Documentation](https://shopify.github.io/flash-list/)
- [React Native Performance Overview](https://reactnative.dev/docs/performance)
