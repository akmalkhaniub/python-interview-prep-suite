# Module 02: P2P Networking, Consensus & Security

This module focuses on peer-to-peer networking (libp2p), consensus loop implementation, and thread safety in Rust node development.

---

## Technical Q&A

### Q1: Describe how a blockchain node uses `libp2p` to perform peer discovery and route messages across a distributed network. Focus on Kademlia DHT and connection multiplexing.
**Answer:**
**libp2p** is a modular P2P networking framework. It abstracts away transport, security, and multiplexing layers into a single runtime engine.

1.  **Peer Discovery & Routing (Kademlia DHT):**
    - libp2p uses a **Kademlia Distributed Hash Table (DHT)** for peer discovery.
    - Each node is identified by a unique `PeerId` (derived from its public key). The network distance between two nodes is measured using the XOR metric over their `PeerId` hashes.
    - To locate a target peer or service, a node queries the closest nodes in its routing table, recursively routing the request closer to the target until the destination node is identified.
2.  **Connection Multiplexing (Yamux / Mplex):**
    - Establishing new TCP or QUIC connections has high handshake latency.
    - **Yamux** (Yet Another Multiplexer) allows libp2p to multiplex multiple independent logical channels (called streams) over a single physical TCP connection.
    - For example, block synchronization, transaction propagation (PubSub/Gossipsub), and RPC queries can run simultaneously over their own isolated streams on a single shared peer-to-peer socket connection.

---

### Q2: Write a conceptual Rust outline of a consensus voting state loop. How do you manage safe thread sharing and state mutation using `std::sync::Arc` and parking_lot Mutex/RwLock?
**Answer:**
In a consensus engine (like Tendermint or PBFT), the voting loop runs on a background thread while network message listeners receive votes from other nodes. We must update the consensus state safely across thread boundaries.

```rust
use std::sync::Arc;
use parking_lot::RwLock; // Faster than std::sync::Mutex (no poison checking, lighter locks)
use std::collections::HashMap;

#[derive(Clone, Debug)]
pub struct Vote {
    pub validator: String,
    pub block_hash: [u8; 32],
    pub round: u64,
}

pub struct ConsensusState {
    pub current_round: u64,
    pub votes_received: HashMap<String, Vote>,
}

pub struct ConsensusEngine {
    // Arc provides thread-safe reference counting
    // RwLock allows multiple concurrent readers, but only one writer
    pub state: Arc<RwLock<ConsensusState>>,
}

impl ConsensusEngine {
    pub fn new() -> Self {
        Self {
            state: Arc::new(RwLock::new(ConsensusState {
                current_round: 0,
                votes_received: HashMap::new(),
            })),
        }
    }

    // Called by network threads when a new vote arrives
    pub fn register_vote(&self, vote: Vote) {
        // Acquire write lock to modify state
        let mut state_writer = self.state.write();
        
        if vote.round == state_writer.current_round {
            state_writer.votes_received.insert(vote.validator.clone(), vote);
            println!("Vote registered. Total votes: {}", state_writer.votes_received.len());
        }
        // Write lock is automatically dropped when state_writer goes out of scope
    }

    // Called by the block production thread to verify threshold
    pub fn check_round_finality(&self, threshold: usize) -> Option<[u8; 32]> {
        // Acquire read lock (non-blocking for other readers)
        let state_reader = self.state.read();
        
        if state_reader.votes_received.len() >= threshold {
            // Count matching hashes and return finalized block hash
            if let Some(first_vote) = state_reader.votes_received.values().next() {
                return Some(first_vote.block_hash);
            }
        }
        None
    }
}
```

---

### Q3: How do you prevent Denial of Service (DoS) attacks caused by memory leaks and panic unwinding inside a Rust blockchain node?
**Answer:**
Although Rust guarantees memory safety, it does not prevent logic-based memory leaks (e.g., keeping unused nodes in memory) or crashes due to unexpected panics.

1.  **Preventing Memory Leak DoS (Transaction Pool Exhaustion):**
    - Attackers can flood the transaction pool with valid signatures but zero account balances.
    - **Mitigation:** Implement strict admission controls (e.g., checking if the sender can afford gas fees before writing the transaction to the memory pool). Limit queue capacities and evict transactions using age or low gas fees when limits are reached.
2.  **Panic Evasion & Catching:**
    - In Rust, a panic (e.g., index out of bounds, division by zero) will unwind the stack, killing the active thread. If this happens in a core block execution loop, the entire node will crash.
    - **Mitigation:**
      - Never use `unwrap()` or `expect()` in production consensus code. Use safe pattern matching (`match`, `if let`) and propagate errors using `Result`.
      - Wrap untrusted smart contract or execution runtimes inside `catch_unwind` block gates:
        ```rust
        use std::panic;
        
        let result = panic::catch_unwind(|| {
            // Execute untrusted runtime bytecode
            execute_vm_tx();
        });
        
        if result.is_err() {
            println!("Execution panicked! Aborting transaction without crashing the host node.");
        }
        ```
