# 01: Kotlin Multiplatform (KMP) Fundamentals

This notebook covers the core mechanics of KMP as required for the ImagineArt interview.

## 1. The Core Philosophy
KMP is not "Write Once, Run Anywhere" (like Flutter). It is **"Share Logic, Keep Native"**.
- **Shared Layer:** Business logic, networking, database, models, validation.
- **Native Layer:** UI (though CMP is changing this), platform-specific APIs.

## 2. Project Structure
A typical KMP project consists of:
- `shared`: Kotlin module containing the logic.
    - `commonMain`: Shared logic.
    - `androidMain`: Android-specific implementations.
    - `iosMain`: iOS-specific implementations.
- `androidApp`: Native Android wrapper.
- `iosApp`: Native iOS wrapper (Xcode project).

## 3. The `expect` / `actual` Mechanism
The cornerstone of platform-specific interaction.

### commonMain (Expect)
```kotlin
// commonMain
expect fun getPlatformName(): String

expect class PlatformLogger() {
    fun log(message: String)
}
```

### androidMain (Actual)
```kotlin
// androidMain
actual fun getPlatformName(): String = "Android ${android.os.Build.VERSION.SDK_INT}"

actual class PlatformLogger {
    actual fun log(message: String) {
        android.util.Log.d("KMP", message)
    }
}
```

### iosMain (Actual)
```kotlin
// iosMain
import platform.UIKit.UIDevice

actual fun getPlatformName(): String = 
    UIDevice.currentDevice.systemName() + " " + UIDevice.currentDevice.systemVersion

actual class PlatformLogger {
    actual fun log(message: String) {
        println("KMP: $message")
    }
}
```

## 4. Key Libraries for KMP
- **Ktor:** Networking.
- **SQLDelight:** Database.
- **Kotlinx.Serialization:** JSON handling.
- **Kermit:** Logging.
- **Koin:** Dependency Injection.

## 5. Potential Interview Questions
1. **Explain the difference between KMP and Flutter/React Native.**
   - *Answer:* KMP focuses on sharing logic while maintaining native performance and UI. Flutter uses its own rendering engine; React Native uses a bridge to native components. KMP compiles to JVM bytecode for Android and Native binaries (via LLVM) for iOS.
2. **How does KMP handle memory management on iOS?**
   - *Answer:* Historically, KMP had a strict memory model for iOS. However, the **New Memory Manager** (enabled by default since Kotlin 1.7.20) has simplified this, allowing objects to be shared between threads without freezing.
3. **What is the `shared` module output for iOS?**
   - *Answer:* It generates an Objective-C framework (universal or XCFramework) that can be imported into Xcode.
