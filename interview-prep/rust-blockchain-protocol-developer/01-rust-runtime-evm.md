# Module 01: Rust Execution Runtimes & State Databases

This module covers the systems-level implementation of virtual machines (EVM/WASM) in Rust, memory management of execution frames, and ledger state database tuning (RocksDB).

---

## Technical Q&A

### Q1: Explain the design of `revm` (Rust EVM interpreter). How does it utilize Rust's type system to achieve memory safety and high-performance gas tracking during bytecode execution?
**Answer:**
`revm` is a pure Rust implementation of the Ethereum Virtual Machine (EVM). It is designed to be fast, modular, and memory-safe without relying on garbage collection.

#### 1. Architectural Highlights:
- **EVM State Isolation:** `revm` separates the virtual machine interpreter from the state database using the `Database` trait.
  ```rust
  pub trait Database {
      type Error;
      fn basic(&mut self, address: Address) -> Result<Option<AccountInfo>, Self::Error>;
      fn code_by_hash(&mut self, code_hash: B256) -> Result<Bytecode, Self::Error>;
      fn storage(&mut self, address: Address, index: U256) -> Result<U256, Self::Error>;
      fn block_hash(&mut self, number: U256) -> Result<B256, Self::Error>;
  }
  ```
- **Interpreter Loop:** The interpreter operates on a stack and a contiguous memory array. Stack operations use a fixed-size array of `U256` elements, preventing runtime heap allocations during execution loops.

#### 2. Gas Tracking & Overflow Prevention:
- Gas checking is performed dynamically before opcode execution. `revm` implements gas calculations using Rust's safe arithmetic (`checked_add`, `checked_mul`) or compiler intrinsic operations.
- If a gas addition overflows or exceeds the block gas limit, the execution immediately halts and returns a structured `InstructionResult::OutOfGas` without causing undefined behavior or panic crashes.

---

### Q2: In Substrate-based blockchains, how does the client decide between executing the runtime using the compiled WASM blob in the blockchain state versus the native binary compiled into the node?
**Answer:**
Substrate provides a unique execution architecture that supports forkless upgrades by storing the blockchain's state transition runtime directly on-chain as a compiled WebAssembly (WASM) blob.

#### Runtime Selection Logic:
1.  **Version Checking:** When a node receives a block, it compares the `spec_version` of the **Native Runtime** (compiled inside the node binary) and the **WASM Runtime** (retrieved from the current block state database key `:code`).
2.  **Execution Path:**
    - If `WASM_spec_version == Native_spec_version`, the node executes the block transition using the **Native Runtime** because it compiles to native assembly and executes significantly faster.
    - If `WASM_spec_version > Native_spec_version` (indicating a runtime upgrade has occurred on-chain but the node software hasn't been upgraded), the node executes the block transition using the **WASM Runtime** inside a WebAssembly executor (like Wasmtime).
    - If `WASM_spec_version < Native_spec_version`, the node executes via WASM to preserve historical consensus compatibility.

```
       [Evaluate Block]
              │
              ▼
    [Compare spec_version]
              │
    ┌─────────┴─────────┐
    ▼ (Versions Equal)  ▼ (WASM Version Greater)
[Native Assembly Run]   [WASM Executor Run]
```

---

### Q3: How do you configure and optimize RocksDB for storing blockchain state histories? Focus on Write Amplification Factor (WAF) mitigation and SST (Sorted String Table) tuning.
**Answer:**
Blockchains generate heavy, random write operations (updating account states) and high volumes of reads (querying block data). This results in high **Write Amplification Factors (WAF)**, causing I/O bottlenecks and SSD wear.

#### Optimization Strategies:
1.  **Block Cache and Memtables:**
    - Increase `write_buffer_size` (memtable size) to allow more writes to accumulate in memory before flushing to disk.
    - Configure `arena_block_size` to optimize memory allocations inside memtables.
2.  **SST File Tuning & Compaction:**
    - RocksDB uses a LSM (Log-Structured Merge) Tree. Use **Leveled Compaction** for blockchain databases because it minimizes read latency.
    - Tune `max_bytes_for_level_base` and `max_bytes_for_level_multiplier` to control level sizing, preventing frequent compactions that drive write amplification.
3.  **Bloom Filters:**
    - Enable Block-based Bloom Filters (e.g., `10 bits_per_key`) on all SST files.
    - This allows read requests to verify if a state key (e.g., account address) is present in an SST file without performing expensive disk seeks, reducing read latency for non-existent keys to near-zero.
