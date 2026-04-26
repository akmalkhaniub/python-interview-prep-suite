# 04: Coroutines & Flows in KMP

Coroutines are the standard for asynchronous programming in Kotlin. In KMP, they play a crucial role in bridging the gap between platforms.

## 1. Structured Concurrency
Always launch coroutines in a `CoroutineScope` tied to a lifecycle (e.g., `ViewModelScope`).
- **Main Thread:** `Dispatchers.Main` (UI updates).
- **IO Thread:** `Dispatchers.Default` (Computational work) or `Dispatchers.IO` (Android/JVM specific for blocking IO). *Note: `Dispatchers.IO` is now available in KMP commonMain since Coroutines 1.7.0.*

## 2. SharedFlow vs StateFlow
- **StateFlow:** Always has a value, emits the last value to new collectors (like LiveData). Perfect for **State**.
- **SharedFlow:** No initial value, can emit multiple values, doesn't retain value by default. Perfect for **Events/Effects**.

## 3. iOS Interoperability
Kotlin Coroutines are exposed as `completion handlers` or `async/await` in Swift.
- Use `SKIE` or `KMP-NativeCoroutines` libraries to make this experience seamless.
- **Cancellation:** Ensure that when a Swift view is dismissed, the underlying Kotlin coroutine is cancelled to avoid memory leaks.

## 4. Flow Operators
- `map`, `filter`: Transformation.
- `flatMapLatest`: Switching between flows (e.g., search queries).
- `combine`, `zip`: Merging flows.
- `catch`, `retry`: Error handling.

## 5. Potential Interview Questions
1. **What is the difference between `launch` and `async`?**
   - *Answer:* `launch` returns a `Job` and is used for fire-and-forget. `async` returns a `Deferred` and is used when you expect a result (via `.await()`).
2. **How do you handle background threading in KMP shared modules?**
   - *Answer:* Use `withContext(Dispatchers.Default)` for CPU-intensive tasks. For networking, Ktor handles threading internally.
3. **What is a "Cold Flow" vs a "Hot Flow"?**
   - *Answer:* A **Cold Flow** (e.g., `flow { ... }`) only starts emitting when there is a collector. A **Hot Flow** (e.g., `StateFlow`, `SharedFlow`) exists independently of collectors and can emit values at any time.
