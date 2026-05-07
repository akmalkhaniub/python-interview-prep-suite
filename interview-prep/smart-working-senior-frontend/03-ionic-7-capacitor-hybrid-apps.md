# Deep Dive: Ionic 7 & Capacitor Hybrid Apps

## 📱 Ionic 7 Architecture
Ionic is framework-agnostic but works exceptionally well with React and Angular.
*   **Web Components:** Ionic components are standard Web Components (Stencil), ensuring high performance and small bundle sizes.
*   **Inline Modals/Popovers:** Improved declarative syntax in v7.
*   **CSS Variables:** The primary way to theme Ionic apps. Understand `::part()` and `--ion-color-primary`.

## ⚡ Capacitor vs Cordova
*   **Capacitor is Native-First:** It doesn't hide the native project (Xcode/Android Studio). You check the `ios` and `android` folders into Git.
*   **Direct Native API Access:** No more "plugin wrappers" for everything. You can write custom Swift/Kotlin code and call it from JS.
*   **Plugins:** Use `@capacitor/camera`, `@capacitor/storage`, `@capacitor/geolocation`.

## 🛠️ Performance & UX
*   **Adaptive Styling:** Ionic automatically changes its look (MD for Android, iOS for iPhone).
*   **Safe Area Insets:** Using CSS to ensure the UI doesn't overlap with notches or home indicators.
    ```css
    padding-top: var(--ion-safe-area-top);
    ```

## 💡 Interview Q&A
**Q: Why choose Capacitor over Flutter or React Native for a "Smart Working" team?**
**A:** Capacitor allows you to leverage existing Web skills (React/Angular) and libraries. You can ship the same code to Web, iOS, and Android. It has a lower learning curve for web developers and better stability for apps that are primarily "content and forms" based.

**Q: How do you handle "Offline Sync" in an Ionic application?**
**A:** Use **SQLite** via Capacitor for local storage. Implement a "Sync Manager" that detects network status (using Capacitor Network plugin) and pushes/pulls changes using a background task or when the app resumes.

## 🛠️ Code Snippet: Calling a Native Plugin
```javascript
import { Camera, CameraResultType } from '@capacitor/camera';

const takePicture = async () => {
  const image = await Camera.getPhoto({
    quality: 90,
    allowEditing: true,
    resultType: CameraResultType.Uri
  });

  var imageUrl = image.webPath;
  // Can now use imageUrl in an <img> tag or upload to server
};
```
