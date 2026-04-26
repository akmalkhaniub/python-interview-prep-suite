# 02: Compose Multiplatform (CMP) UI

Compose Multiplatform brings the Jetpack Compose declarative UI framework to Android, iOS, Desktop (JVM), and Web.

## 1. How CMP Works on iOS
CMP uses **Skia** (the same rendering engine Flutter uses) to draw the UI on iOS.
- It provides a `ComposeUIViewController` to wrap the Compose UI for usage in SwiftUI or UIKit.
- **Interoperability:** You can embed SwiftUI views inside Compose using `UIKitView` or `SwiftUI` wrappers.

## 2. Common UI Patterns
### The `Theme` Wrapper
```kotlin
// commonMain
@Composable
fun AppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colors = if (isSystemInDarkTheme()) darkColors() else lightColors(),
        typography = Typography,
        shapes = Shapes,
        content = content
    )
}
```

### Resource Handling
Using the `compose-resources` library (bundled with CMP).
```kotlin
// commonMain
Image(
    painter = painterResource(Res.drawable.logo),
    contentDescription = null
)
Text(stringResource(Res.string.app_name))
```

## 3. Navigation in CMP
Since there is no "Fragment" or "Activity" in the shared layer, navigation is handled differently:
- **Decompose:** A popular library for routing and lifecycle management.
- **Voyager:** A pragmatic navigation library for Compose Multiplatform.
- **Jetpack Navigation (Experimental):** Google is working on a multiplatform version of the official navigation component.

## 4. Performance Considerations
- **iOS Frame Rate:** Ensure you are testing on actual devices. The simulator might show stuttering that isn't present on hardware.
- **Skia Overhead:** Be aware that using Skia means the app binary size will increase (usually ~5-10MB).

## 5. Potential Interview Questions
1. **Is Compose Multiplatform stable for iOS?**
   - *Answer:* As of recent updates, it is in **Beta**, meaning it's production-ready but some APIs might still change. Many companies (like ImagineArt) are already using it.
2. **How do you handle safe area insets on iOS with CMP?**
   - *Answer:* You can use `WindowInsets.safeDrawing` or `Modifier.windowInsetsPadding()` which works across platforms if configured correctly.
3. **What is the difference between `@Composable` on Android vs Multiplatform?**
   - *Answer:* The core logic is the same, but you must avoid platform-specific imports (like `android.graphics.*`) inside common Composable functions.
