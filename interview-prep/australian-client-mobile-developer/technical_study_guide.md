# Technical Study Guide: Mobile Front-End Developer (Flutter/RN Focus)

## 1. Flutter & BLoC Mastery
- **BLoC Core Concepts:**
    - Events vs. States.
    - `BlocProvider`, `BlocListener`, `BlocBuilder`, and `BlocConsumer`.
    - `Cubit` vs. `Bloc`: When to use which?
- **Dart Streams:** Understanding `StreamController`, `broadcast streams`, and stream transformations.
- **Dependency Injection:** Using `get_it` or `Provider` alongside BLoC.

## 2. Flutter Web
- **Rendering Engines:** **HTML** (fast load, lower fidelity) vs. **CanvasKit** (slower load, pixel-perfect).
- **Responsive Layouts:** `LayoutBuilder`, `MediaQuery`, and the `ResponsiveFramework` package.
- **Web Integrations:** Handling browser-specific APIs (URL routing, shared preferences on web).

## 3. React Native & Expo
- **Expo SDK:** Using Expo Go vs. Development Builds.
- **State Management:** Reviewing **Redux** or **Zustand** (as an alternative to BLoC in the RN world).
- **Navigation:** **Expo Router** (file-based) vs. **React Navigation**.

## 4. API Integration & GraphQL
- **Dio/Http:** Advanced configurations (Interceptors, Retries).
- **GraphQL:**
    - **Ferry** or **Gql** for Flutter.
    - **Apollo Client** for React Native.
- **JSON Serialization:** `json_serializable` and `freezed` for type-safe models in Dart.

## 5. Testing & Performance
- **Flutter Testing:**
    - Unit tests for BLoCs (using the `bloc_test` package).
    - Widget tests for UI components.
- **Profiling:**
    - **Flutter DevTools:** CPU Profiler, Memory allocation, and the widget inspector.
    - **RN Performance:** Identifying JSI bridge bottlenecks.

## 6. Soft Skills & AEST Context
- **Australian Work Culture:** Direct communication, emphasis on work-life balance, and proactive problem-solving.
- **Remote Collaboration:** Mastery of Slack, Zoom, and Jira in an Agile environment.

## Resources to Review
- [Bloc Library Documentation](https://bloclibrary.dev/)
- [Flutter Web: Getting Started](https://docs.flutter.dev/get-started/web)
- [Expo Documentation](https://docs.expo.dev/)
- [Clean Architecture for Flutter (ResoCoder)](https://resocoder.com/flutter-clean-architecture-tdd/)
