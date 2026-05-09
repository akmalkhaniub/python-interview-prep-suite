# Technical Study Guide: React Native Lead (Tether)

## 1. Advanced React Native & Expo
- **Expo Managed vs. Bare Workflow:** Understanding when to "eject" or use Development Builds for native C++ modules.
- **New Architecture (JSI):** How to use C++ directly in React Native for high-performance crypto libraries.
- **Redux Toolkit (RTK):** Using `createAsyncThunk` and `RTK Query` for managing wallet data.
- **Performance:** Memoization, FlashList, and bridge bottleneck analysis.

## 2. Crypto & DeFi Mobile Engineering
- **Cryptography:** Understanding `secp256k1` (Bitcoin/Ethereum curve) and how to handle it in RN.
- **BIP Standards:**
    - **BIP-39:** Mnemonic seed phrases.
    - **BIP-44:** Multi-account hierarchy for deterministic wallets.
- **Web3 Integration:** Using `ethers.js` or `web3.js` inside React Native.

## 3. Native & C++ Development
- **JSI (JavaScript Interface):** Writing C++ HostObjects to expose high-speed native logic to JS.
- **iOS (Swift/Obj-C):** Implementing Secure Enclave access for key storage.
- **Android (Kotlin/Java):** Using Keystore for hardware-backed security.

## 4. Full-Stack Node.js for Mobile
- **API Design:** Building RESTful and WebSocket endpoints for wallet history and notifications.
- **Authentication:** JWT, OAuth2, and biometrics-based auth flows.
- **Microservices:** Understanding how Tether might structure its data services to power global apps.

## 5. CI/CD & Leadership
- **EAS Build/Submit:** Automating the deployment pipeline for Expo.
- **Code Review:** Setting standards for crypto-critical code (security-first reviews).
- **Mentorship:** Strategies for growing junior and mid-level engineers in a remote setting.

## 6. Security Best Practices
- **Persistence:** Securely storing tokens and sensitive data (MMKV with encryption).
- **SSL Pinning:** Preventing Man-in-the-Middle attacks on sensitive crypto transactions.
- **Obfuscation:** Using ProGuard and DexGuard to protect the binary.

## Resources to Review
- [Tether Official Documentation](https://tether.to/en/transparency/)
- [React Native JSI/TurboModules Guide](https://reactnative.dev/docs/the-new-architecture-intro)
- [Mastering Bitcoin: The Open Blockchain (Wallets Chapter)](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc)
- [Expo EAS Documentation](https://docs.expo.dev/eas/)
