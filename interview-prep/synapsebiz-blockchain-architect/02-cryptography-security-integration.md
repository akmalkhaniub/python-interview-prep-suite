# Module 02: Cryptography, Security & Integration

This module focuses on applied cryptography (ECDSA, Ed25519), hardware key management, event-driven backend service integration, and threat modeling for distributed networks.

---

## Technical Q&A

### Q1: Compare ECDSA (using the secp256k1 curve) and Ed25519 digital signature schemes. Why is Ed25519 preferred for modern, high-throughput systems?
**Answer:**

1. **ECDSA (Elliptic Curve Digital Signature Algorithm - secp256k1):**
   - **Curve:** Koblitz curve (used in Bitcoin and Ethereum).
   - **Signature Generation:** Historically required a cryptographically secure random number $k$. If the same $k$ is ever reused across two different signatures, an attacker can mathematically extract the private key. EIP-2 / RFC 6979 mitigates this by generating $k$ deterministically.
   - **Verification:** Computationally slower and requires complex mathematical implementations prone to side-channel attacks.

2. **Ed25519 (EdDSA using Curve25519):**
   - **Curve:** Montgomery / Edwards curve (used in Solana, NEAR, and Polkadot).
   - **Signature Generation:** Secure by design. It generates the signing nonce deterministically, eliminating the risk of private key leakage due to entropy failures.
   - **Performance:** Extremely fast signature generation and verification. Ed25519 can process tens of thousands of signatures per second on standard CPU cores, significantly increasing blockchain validation throughput.
   - **Security:** Malleability protection (signatures cannot be altered without breaking validity) and high resistance to side-channel timing attacks.

---

### Q2: Explain how secure key management is achieved in enterprise blockchain architectures. Compare Hardware Security Modules (HSMs) and Multi-Party Computation (MPC) thresholds.
**Answer:**
Private keys are the single point of failure in blockchain applications. If a private key is exposed, the assets are gone, and actions cannot be undone.

1. **Hardware Security Modules (HSMs) & KMS:**
   - **How it works:** Private keys are generated and stored inside physical, tamper-resistant hardware chips. The key never leaves the boundary of the hardware. The backend application sends a hash to the HSM, which signs the payload internally and returns the signature.
   - **Pros:** Maximum physical security; certified standards (FIPS 140-2 Level 3/4).
   - **Cons:** Single point of failure (if the physical unit is destroyed without backup, keys are lost). Latency overhead when scaling to thousands of parallel transactions.

2. **Multi-Party Computation (MPC) Threshold Signatures:**
   - **How it works:** The private key is never generated in one place. Instead, multiple independent parties (nodes/servers) generate "key shares" collaboratively. To sign a transaction, a threshold of shares (e.g., $t$ out of $n$) must interact mathematically to construct the signature without ever reassembling the raw private key.
   - **Pros:** No single point of failure. If one server is compromised, the attacker only gets a useless key share. Enables flexible access control policies programmatically.
   - **Cons:** Requires complex cryptographic coordination protocols (e.g., GG18/GG20 schemes) which add network message roundtrips between signing parties.

---

### Q3: When integrating a backend backend microservice with a blockchain node via JSON-RPC, how do you handle event listening, block re-organizations (re-orgs), and account Nonce management?
**Answer:**

1. **Event Listening & Log Parsing:**
   - Instead of polling the RPC node via standard HTTP, use **WebSockets** to subscribe to log filters (`eth_subscribe` with `logs`). The node pushes event receipts in real time.
   - Parse event topics: Topic 0 is always the Keccak-256 hash of the event signature (e.g., `Transfer(address,address,uint256)`).

2. **Handling Block Re-organizations (Re-orgs):**
   - **The Problem:** In probabilistic chains, a temporary network split can cause the blockchain to fork. The chain with more accumulated work becomes the main chain, discarding blocks from the shorter fork. Any event parsed in a discarded block is invalidated.
   - **Mitigation:**
     - Implement a **Block Confirmation Threshold** (e.g., wait until a block is at least 12 blocks deep before executing backend actions like shipping goods).
     - Keep an internal transaction table with states: `Pending`, `Unconfirmed`, `Confirmed`.
     - Listen to block headers. If a block hash changes for a given block height, rollback the state changes associated with the discarded block (e.g., reverting to `Unconfirmed`) and re-evaluate the correct chain.

3. **Account Nonce Management:**
   - **The Problem:** Blockchains require transactions from an account to be executed in sequential order based on a Nonce (transaction counter). If you send 5 transactions concurrently, the node will reject them if nonces are skipped or duplicated.
   - **Mitigation:**
     - Maintain an in-memory **Nonce Manager** in your backend microservice (e.g., using Redis locks or database transactions).
     - Keep track of the `next_nonce` in Redis. Increment it locally when sending transactions. Do not rely on querying the node's `getTransactionCount` pending state, as the node's queue might have synchronization latency.
     - If a transaction is stuck (gas prices spike), submit a replacement transaction using the *same nonce* but with at least a 10% higher gas fee (EIP-1559 `maxPriorityFeePerGas`) to overwrite it.

---

### Q4: Define the Reentrancy vulnerability in Solidity and explain how to prevent it.
**Answer:**
A **Reentrancy** vulnerability occurs when a smart contract sends funds to an untrusted contract (via `call`) before updating its state variables (such as user balances). The receiving contract can hijack the execution flow by recursively calling the withdrawal function again before the first execution updates the balance to zero.

#### Vulnerable Code:
```solidity
contract VulnerableBank {
    mapping(address => uint256) public balances;

    function withdraw() public {
        uint256 amount = balances[msg.sender];
        require(amount > 0);
        
        // VULNERABILITY: External call happens before state update!
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success);
        
        balances[msg.sender] = 0;
    }
}
```

#### Prevention Methods:
1. **Checks-Effects-Interactions Pattern:** Always perform checks first (require statements), modify state variables next (effects), and only then execute external calls (interactions):
   ```solidity
   function withdraw() public {
       uint256 amount = balances[msg.sender];
       require(amount > 0);
       
       // Effect
       balances[msg.sender] = 0;
       
       // Interaction
       (bool success, ) = msg.sender.call{value: amount}("");
       require(success);
   }
   ```
2. **Reentrancy Guard (Mutex):** Use inheritance modifiers like OpenZeppelin's `nonReentrant` which sets a lock state variable during invocation and reverts if the function is entered recursively.
