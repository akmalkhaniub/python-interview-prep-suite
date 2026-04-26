# Interview Prep: Software Engineer — Mobile (Kotlin Multiplatform) @ ImagineArt

Welcome to the preparation suite for the **ImagineArt KMP Engineer** role. This role emphasizes **ownership**, **speed**, and **high-quality multiplatform engineering**.

## Preparation Roadmap

### 1. Kotlin Multiplatform (KMP) & Compose Multiplatform (CMP)
- [ ] Shared Module Structure (`expect`/`actual` mechanism)
- [ ] Dependency Management in KMP (version catalogs, multiplatform libraries)
- [ ] Compose Multiplatform for iOS (Current status, limitations, and performance)
- [ ] Resources sharing (images, strings, fonts) in CMP

### 2. Architecture & State Management
- [ ] **Clean Architecture:** Domain-driven design in shared modules.
- [ ] **MVI (Model-View-Intent):** Handling side effects and state transitions.
- [ ] **StateFlow/SharedFlow:** Integration with UI layers.
- [ ] **Decompose:** Navigation in Multiplatform apps (popular for CMP).

### 3. Asynchronous Programming
- [ ] Structured Concurrency in Coroutines.
- [ ] Flow API (Intermediate operators, backpressure).
- [ ] Coroutines in KMP (Interoperability with Swift/Objective-C `async/await`).

### 4. Dependency Injection
- [ ] **Koin:** The standard for KMP due to its Kotlin-first nature.
- [ ] **Manual DI:** When and why to use it in smaller modules.

### 5. Product & Ownership (Soft Skills)
- [ ] How to handle "Problem → Release" cycle.
- [ ] Experience with GenAI products (ImagineArt specific).
- [ ] Proactive performance monitoring and bug fixing.

## Study Material (Notebooks)
1. [KMP Fundamentals](./01-KMP-Fundamentals.md)
2. [Compose Multiplatform UI](./02-CMP-UI.md)
3. [MVI & State Management](./03-MVI-State-Management.md)
4. [Asynchronous Patterns](./04-Coroutines-Flows.md)
5. [Clean Architecture for KMP](./05-Clean-Architecture.md)

---

## ImagineArt Context
- **Product:** AI Image Generation.
- **Scale:** $35M+ ARR, 100M+ impressions.
- **Culture:** Zero bureaucracy, high speed, zero funding (bootstrapped excellence).
