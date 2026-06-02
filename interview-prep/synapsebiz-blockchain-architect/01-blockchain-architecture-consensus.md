# Module 01: Blockchain Architecture & Consensus

This module focuses on distributed ledger frameworks, consensus mechanisms, smart contract lifecycle, and state-transition models.

---

## Technical Q&A

### Q1: Compare the architectural designs and target use cases of Ethereum/EVM, Hyperledger Fabric, and Substrate/Polkadot.
**Answer:**

| Feature | Ethereum (EVM) | Hyperledger Fabric | Substrate (Polkadot) |
| :--- | :--- | :--- | :--- |
| **Network Type** | Public, Permissionless. | Private, Permissioned. | Hybrid / Public (Customizable). |
| **Consensus Mechanism** | Proof of Stake (PoS) / Gas-metered. | Pluggable (Raft, PBFT). | BABE (block production) & GRANDPA (finality). |
| **State Machine** | Ethereum Virtual Machine (EVM) bytecode. | Chaincode (Docker containers running Go, Java, Node.js). | WebAssembly (WASM) runtime built with Rust. |
| **Data Privacy** | Transparent. Requires zero-knowledge proofs (ZKP) for private states. | Private Data Collections, Channels. | Private states can be built via custom pallets. |
| **Target Use Case** | Global DeFi, NFTs, and public decentralized applications. | B2B Enterprise consortia, supply chain, and trade finance. | Specialized application-specific chains (AppChains). |

**Architectural Rationale:**
- **Ethereum (EVM)** is optimized for trustless, censorship-resistant environments where anyone can read, write, and deploy code, using Gas to prevent infinite loop exploits.
- **Hyperledger Fabric** is designed for corporate networks where participants are known and verified. It separates transaction execution ("endorsement") from transaction ordering, enabling high throughput and fine-grained privacy controls via segregated channels.
- **Substrate** provides a modular framework to build custom blockchains. By compiling the runtime state transition logic to WASM, it allows chains to perform forkless runtime upgrades by storing the new runtime WASM code directly in the blockchain state.

---

### Q2: Detail the consensus mechanism trade-offs between Proof of Stake (PoS) and Practical Byzantine Fault Tolerance (PBFT). How do finality guarantees differ?
**Answer:**

1. **Proof of Stake (PoS):**
   - **How it works:** Block proposers and validators are selected based on the size of their locked token stake. Validators vote on block validity, risking slashing (token destruction) if they sign conflicting or invalid states.
   - **Liveness vs. Safety:** PoS prioritizing liveness (e.g., Gasper in Ethereum). Blocks are produced continuously, but **finality is probabilistic** or occurs in epochs (e.g., finality checkpoint after 32 slots).
   - **Scale:** Scales to thousands of nodes.

2. **Practical Byzantine Fault Tolerance (PBFT):**
   - **How it works:** Network nodes reach agreement through a series of multi-phase voting rounds (Pre-prepare, Prepare, Commit). It requires a known, static list of validators and can tolerate up to $f$ malicious/faulty nodes where total nodes $N \ge 3f + 1$.
   - **Liveness vs. Safety:** PBFT prioritizes safety. It offers **immediate finality**: once a block receives commits from $> 2/3$ of the validators, the transaction state is permanently finalized and cannot be re-organized.
   - **Scale:** Poor scalability. The messaging complexity is $O(N^2)$, limiting the validator count to a few dozen nodes before latency degrades exponentially.

---

### Q3: Explain the Proxy patterns used to implement upgradeable Smart Contracts in Solidity. How does a UUPS (Universal Upgradeable Proxy Standard) proxy differ from a Transparent Proxy?
**Answer:**
Solidity smart contracts are immutable once deployed. To upgrade business logic, developers use **Proxy Patterns** utilizing the `delegatecall` EVM opcode.

```
[User Request] ──► [Proxy Contract (Stores State Variables)]
                         │ (delegatecall - executes code in logic context)
                         ▼
                   [Logic / Implementation Contract (Executable Code)]
```
The Proxy contract delegates all execution to the Logic contract while maintaining the persistent storage variables (state) on the Proxy contract itself.

**UUPS vs. Transparent Proxy Pattern:**

1. **Transparent Proxy Pattern (TPP):**
   - **How TPP works:** The upgrade logic (the function to change the logic contract address) resides inside the Proxy contract.
   - **The Problem TPP solves:** Function selector clashes (where a function signature in the logic contract matches the upgrade function signature of the proxy).
   - **TPP Disadvantage:** High deployment and transaction costs because every call must check if the caller is the proxy admin, requiring extra storage reads.

2. **UUPS (Universal Upgradeable Proxy Standard - EIP-1822):**
   - **How UUPS works:** The upgrade logic is placed inside the **Logic/Implementation contract** itself, not in the Proxy contract.
   - **UUPS Advantage:** Much cheaper gas costs. Since the proxy has no upgrade logic, calls do not need to query admin status.
   - **UUPS Risk:** If you deploy a new logic contract that lacks the upgrade function, you **permanently lock** the contract, and it can never be upgraded again.

---

### Q4: Compare the UTXO (Unspent Transaction Output) model and the Account-based model. How do they affect transaction concurrency and double-spending protection?
**Answer:**

1. **UTXO Model (e.g., Bitcoin, Cardano):**
   - **Mechanism:** The ledger is a database of unspent transaction outputs. A transaction consumes existing UTXOs as inputs and generates new UTXOs as outputs.
   - **Concurrency:** Highly parallelizable. Since transactions specify the exact UTXOs they spend, independent transactions can be processed concurrently without locking the global state.
   - **Double-Spending:** Easy to prevent. The network checks if the input UTXO is already marked as "spent."

2. **Account-Based Model (e.g., Ethereum, Solana):**
   - **Mechanism:** The ledger maintains a state of accounts, each with a balance and a state storage database. Transactions debit one account and credit another, updating the global state.
   - **Concurrency:** Sequential / Serialized (per account). Transactions touching the same account must be ordered sequentially to prevent race conditions. Systems like Solana use access lists to run independent account transactions in parallel.
   - **Double-Spending:** Prevented using a transaction counter (Nonce) per account, ensuring transactions are executed in a strict chronological sequence.
