# Technical Architecture

KnexCoin implements a novel hybrid consensus mechanism combining Proof-of-Bandwidth (PoB) with Proof-of-Stake (PoS), built on a block-lattice DAG structure.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Layer                              │
├──────────────┬──────────────┬──────────────┬───────────────────┤
│   Browser    │     PWA      │   NFC Card   │    Merchant       │
│  Extension   │   Wallet     │   Payments   │    Dashboard      │
└──────┬───────┴──────┬───────┴──────┬───────┴────────┬──────────┘
       │              │              │                │
       └──────────────┴──────┬───────┴────────────────┘
                             │
                      ┌──────▼──────┐
                      │   API Layer  │
                      │  (REST/RPC)  │
                      └──────┬───────┘
                             │
       ┌─────────────────────┼─────────────────────┐
       │                     │                     │
┌──────▼──────┐       ┌──────▼──────┐       ┌──────▼──────┐
│  Flask API  │       │  Rust Node  │       │   gRPC      │
│   (8080)    │       │   (7076)    │       │  (9090)     │
└──────┬──────┘       └──────┬──────┘       └──────┬──────┘
       │                     │                     │
       └─────────────────────┼─────────────────────┘
                             │
                      ┌──────▼──────┐
                      │   Storage    │
                      │  (RocksDB)   │
                      └─────────────┘
```

---

## Block-Lattice DAG

Unlike traditional blockchains with a single chain, KnexCoin uses a **block-lattice** where each account maintains its own blockchain.

### Structure

```
Alice's Chain                    Bob's Chain
    │                                │
    ▼                                ▼
┌─────────┐                    ┌─────────┐
│ OPEN    │                    │ OPEN    │
│ +1000   │                    │ +500    │
└────┬────┘                    └────┬────┘
     │                              │
     ▼                              ▼
┌─────────┐    ─ ─ ─ ─ ─ ─    ┌─────────┐
│ SEND    │─ ─ ─ pending ─ ─ ▶│ RECEIVE │
│ -100    │    100 KNEX       │ +100    │
└────┬────┘                    └────┬────┘
     │                              │
     ▼                              ▼
┌─────────┐                    ┌─────────┐
│ RECEIVE │                    │ SEND    │
│ +50     │                    │ -25     │
└─────────┘                    └─────────┘
```

### Benefits

- **Instant confirmations** - No waiting for block inclusion
- **Parallel processing** - Accounts don't block each other
- **No double-spending** - Each send creates unique pending block
- **Prunable history** - Old blocks can be archived

---

## Consensus Mechanism

### Proof-of-Bandwidth (PoB)

Validators prove they contribute real network bandwidth:

```
┌─────────────────────────────────────────┐
│           Bandwidth Challenge           │
├─────────────────────────────────────────┤
│ 1. Network issues random challenge      │
│ 2. Validator must relay challenge data  │
│ 3. Latency and throughput measured      │
│ 4. Results recorded on-chain            │
│ 5. Rewards proportional to bandwidth    │
└─────────────────────────────────────────┘
```

### Proof-of-Stake (PoS)

Economic stake provides security against attacks:

```
Validator Selection Probability:

P(v) ∝ Stake(v) × Bandwidth(v) × (1 / Latency(v))
```

### Combined PoB + PoS

| Factor | Weight | Purpose |
|--------|--------|---------|
| Stake | 40% | Economic security |
| Bandwidth | 35% | Network capacity |
| Uptime | 15% | Reliability |
| Latency | 10% | Performance |

---

## Block Structure

### Block Fields

```rust
pub struct Block {
    pub block_type: BlockType,      // SEND, RECEIVE, OPEN, CHANGE, BANDWIDTH
    pub account: [u8; 32],          // Account public key
    pub previous: [u8; 32],         // Previous block hash (frontier)
    pub representative: [u8; 32],   // Validator representative
    pub balance: u128,              // Account balance after block
    pub link: [u8; 32],             // Context-dependent link field
    pub signature: [u8; 64],        // Ed25519 signature
    pub work: u64,                  // PoW nonce (spam prevention)
    pub height: u64,                // Block height in account chain
    pub timestamp: u64,             // Unix timestamp
}
```

### Block Types

| Type | Link Field Contains | Purpose |
|------|---------------------|---------|
| `OPEN` | Source block hash | Create account |
| `SEND` | Destination account | Transfer funds |
| `RECEIVE` | Source send block | Claim pending |
| `CHANGE` | New representative | Update validator |
| `BANDWIDTH` | Proof data hash | Record PoB proof |

---

## Cryptographic Primitives

### Current Implementation

| Component | Algorithm | Size |
|-----------|-----------|------|
| Signatures | Ed25519 | 64 bytes |
| Hashing | Blake2b-256 | 32 bytes |
| Addresses | Base32 | 60 chars |
| Key derivation | PBKDF2-SHA512 | 32 bytes |

### Planned Quantum-Resistant

| Component | Algorithm | Size |
|-----------|-----------|------|
| Primary Signatures | FALCON-512 | 666 bytes |
| Backup Signatures | Dilithium-65 | 3,293 bytes |
| Key Encapsulation | Kyber-768 | 1,088 bytes |
| Hashing | SHA3-256 + BLAKE3 | 32 bytes |

---

## Transaction Flow

### Send Transaction

```
1. User initiates send
        │
        ▼
2. Create SEND block
   - Previous: account frontier
   - Balance: current - amount
   - Link: recipient address
        │
        ▼
3. Sign with Ed25519
        │
        ▼
4. Compute PoW nonce
        │
        ▼
5. Broadcast to network
        │
        ▼
6. SPVT validators attest
        │
        ▼
7. Block confirmed
   - Pending created for recipient
```

### Receive Transaction

```
1. Recipient sees pending
        │
        ▼
2. Create RECEIVE block
   - Previous: account frontier
   - Balance: current + pending
   - Link: source SEND hash
        │
        ▼
3. Sign with Ed25519
        │
        ▼
4. Compute PoW nonce
        │
        ▼
5. Broadcast to network
        │
        ▼
6. Block confirmed
   - Pending cleared
   - Balance updated
```

---

## Validator Registry

### Registration Requirements

| Parameter | Value |
|-----------|-------|
| Minimum Stake | 10,000 KNEX |
| Minimum Bandwidth | 100 Mbps |
| Minimum Uptime | 95% |
| Max Validators | 100 |

### Validator Data Structure

```rust
pub struct ValidatorInfo {
    pub stake: u128,
    pub is_active: bool,
    pub reputation: u8,         // 0-100
    pub last_seen: u64,
    pub uptime_seconds: u64,
    pub validations_count: u64,
    pub failures_count: u64,
}
```

### Reputation Scoring

```
Reputation = (Uptime% × 0.4) + (Success% × 0.4) + (Bandwidth% × 0.2)

Where:
- Uptime%: Seconds online / Total seconds
- Success%: Successful validations / Total validations
- Bandwidth%: Measured / Required bandwidth
```

---

## Network Architecture

### P2P Topology

```
    ┌─────────────┐
    │  Bootstrap  │
    │    Nodes    │
    └──────┬──────┘
           │
    ┌──────┴──────┐
    │             │
┌───▼───┐   ┌────▼────┐
│Validator│   │Validator│
│   A    │───│    B    │
└───┬────┘   └────┬────┘
    │             │
    │   ┌─────┐   │
    └──▶│Light│◀──┘
        │Client│
        └─────┘
```

### Message Types

| Message | Purpose |
|---------|---------|
| `ANNOUNCE` | Advertise node presence |
| `BLOCK` | Propagate new block |
| `VOTE` | Validator attestation |
| `CHALLENGE` | Bandwidth proof request |
| `RESPONSE` | Bandwidth proof result |

### Gossip Protocol

- **Block propagation:** GossipSub with validator priority
- **Vote aggregation:** Threshold signatures per tier
- **Peer discovery:** Kademlia DHT

---

## Storage Layer

### RocksDB Schema

| Column Family | Key | Value |
|---------------|-----|-------|
| `accounts` | Public key | Account state |
| `blocks` | Block hash | Block data |
| `frontiers` | Account key | Latest block hash |
| `pending` | Account + Hash | Pending amount |
| `validators` | Public key | Validator info |
| `bandwidth` | Public key | PoB proofs |

### Account State

```rust
pub struct Account {
    pub public_key: [u8; 32],
    pub balance: u128,
    pub frontier: [u8; 32],     // Latest block hash
    pub block_count: u64,
    pub representative: [u8; 32],
    pub modified: u64,          // Last modification time
}
```

---

## Security Model

### Attack Vectors & Mitigations

| Attack | Mitigation |
|--------|------------|
| Double-spend | SPVT tier consensus |
| Sybil | Stake + bandwidth requirements |
| Eclipse | Peer diversity enforcement |
| Spam | PoW per transaction |
| Long-range | Checkpoint anchoring |

### Slashing Conditions

| Violation | Penalty |
|-----------|---------|
| Bandwidth spoofing | 100% stake |
| Collusion | 75% stake |
| Attestation fraud | 50% stake |
| Downtime | Reputation decay |

---

## Performance Characteristics

| Metric | Value |
|--------|-------|
| Theoretical TPS | 10,000+ |
| Confirmation Time | < 1 second |
| Block Size | ~200-500 bytes |
| State Size | ~100 bytes/account |
| Bandwidth Overhead | Minimal (per-account) |

---

## Further Reading

- [SPVT Validation Tiers](SPVT-Validation-Tiers)
- [Tokenomics](Tokenomics)
- [Validator Guide](Validator-Guide)
- [API Reference](API-Reference)
