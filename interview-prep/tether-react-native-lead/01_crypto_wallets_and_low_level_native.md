# Deep Dive: Crypto Wallets & Low-Level Native Modules

Working at Tether requires a unique blend of high-level UI/UX and low-level security and performance logic.

## 1. High-Performance Crypto Logic (C++)
*   **Why C++?** Cryptographic operations (like transaction signing) are computationally expensive and require deterministic performance.
*   **JSI (JavaScript Interface):** JSI allows the JS engine to call C++ functions directly without the overhead of JSON serialization over the "bridge."
*   **Case Study:** Integrating a library like `libsecp256k1` as a JSI module to sign transactions in < 1ms.

## 2. Secure Storage & Key Management
*   **The Golden Rule:** Never store private keys in plain text.
*   **iOS (Secure Enclave):** Storing a hardware-backed key that never leaves the chip, using Swift to expose it to the RN layer.
*   **Android (Hardware Security Module):** Using the Android Keystore to sign messages without revealing the private key.
*   **RN Implementation:** Using `react-native-keychain` or custom native modules to interact with these hardware features.

## 3. Real-Time Crypto Data (WebSockets)
*   **The Problem:** Traditional REST polling is too slow for volatile crypto prices.
*   **The Solution:** Persistent WebSocket connections to Tether or third-party exchanges (Binance, Coinbase).
*   **RN Pattern:**
    - Opening the socket in a `useEffect` or a dedicated WebSocket Service.
    - Dispatching price updates to **Redux** for immediate UI reflection.
    - Handling reconnections and "heartbeat" checks for reliability.

## 4. Wallet Recovery & BIP Standards
*   **BIP-39:** Implementing the word-list mnemonic (12-24 words) recovery flow.
*   **Validation:** Ensuring the checksum is valid before allowing the user to proceed.
*   **UI/UX:** Preventing screenshots of the seed phrase and forcing the user to verify the words in order.

## Interview Questions
1.  "How would you explain the benefit of JSI over the standard Bridge to a developer who only knows React?"
2.  "What is the most secure way to store a BIP-39 seed phrase on a mobile device?"
3.  "How do you handle the race condition where a WebSocket price update arrives while the user is initiating a transaction?"
4.  "Describe your experience writing custom C++ or Objective-C code for a React Native project."
