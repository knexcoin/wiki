# KnexCoin

<p align="center">
  <img src="https://raw.githubusercontent.com/knexcoin/wiki/main/assets/knex-logo.png" alt="KnexCoin Logo" width="200"/>
</p>

<p align="center">
  <strong>Proof-of-Bandwidth Blockchain for the Real Economy</strong>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#documentation">Documentation</a> •
  <a href="#contributing">Contributing</a>
</p>

---

## What is KnexCoin?

**KnexCoin (KNEX)** is a next-generation cryptocurrency that solves the blockchain trilemma through a novel **Proof-of-Bandwidth (PoB)** consensus mechanism combined with Proof-of-Stake. Unlike traditional cryptocurrencies, KnexCoin incentivizes real infrastructure contribution rather than pure capital lockup.

### Key Innovations

- **🌐 Proof-of-Bandwidth** — Validators earn rewards for providing measurable network bandwidth
- **💸 Zero-Fee Transactions** — Frictionless value transfer without intermediaries
- **🏪 Merchant-Driven Price Discovery** — No exchange listings; price emerges from real commerce
- **🔒 SPVT Security** — Stake-Proportional Validation Tiers scale security with transaction value
- **⚡ Block-Lattice DAG** — Near-instant confirmations with parallel transaction processing

---

## Features

| Feature | Description |
|---------|-------------|
| **Zero Fees** | Transactions are free, secured by bandwidth contribution |
| **Instant Confirmation** | Block-lattice architecture enables sub-second finality |
| **Fixed Supply** | Hard cap of 210 million KNEX with halving emission |
| **NFC Payments** | Tap-to-pay with physical KnexCoin cards |
| **Merchant Focus** | Built for real-world commerce, not speculation |
| **Quantum-Safe** | NIST post-quantum cryptography (FALCON, Kyber, Dilithium) from Genesis |
| **Web3 Mail** | Encrypted decentralized messaging via KnexMail |

---

## Quick Start

### Install the Wallet

```bash
# Browser Extension
# Visit: https://knexpay.com/extension

# Or use the PWA
# Visit: https://knexpay.com
```

### Run a Node

```bash
# Clone the repository
git clone https://github.com/knexcoin/knexcoin.git
cd knexcoin

# Build the Rust node
cd "Knex Project/node"
cargo build --release

# Start the node
./target/release/knex-node start
```

### Run the Backend API

```bash
cd KnexDAG
pip install -r requirements.txt
python -m src.api
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Client Layer                            │
├─────────────┬─────────────┬─────────────┬──────────────────┤
│  Extension  │    PWA      │  NFC Card   │    AyeDEX        │
└──────┬──────┴──────┬──────┴──────┬──────┴────────┬─────────┘
       └─────────────┴──────┬──────┴───────────────┘
                            │
                     ┌──────▼──────┐
                     │  REST API   │
                     └──────┬──────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
       ┌──────▼──────┐ ┌────▼────┐ ┌──────▼──────┐
       │ Flask API   │ │  Rust   │ │   gRPC      │
       │   (8080)    │ │  Node   │ │  (9090)     │
       └──────┬──────┘ │ (7076)  │ └──────┬──────┘
              │        └────┬────┘        │
              └─────────────┼─────────────┘
                            │
                     ┌──────▼──────┐
                     │   RocksDB   │
                     └─────────────┘
```

### Tech Stack

| Layer | Technology |
|-------|------------|
| **Node** | Rust, Tokio, Axum, RocksDB |
| **Backend** | Python, Flask, SQLite |
| **Frontend** | SvelteKit, TypeScript, Vite |
| **Crypto** | FALCON, Kyber, Dilithium (post-quantum) + Ed25519, Blake2b |

---

## Tokenomics

| Parameter | Value |
|-----------|-------|
| **Max Supply** | 210,000,000 KNEX |
| **Genesis** | 21,000,000 KNEX (10%) |
| **PoB Emission** | 189,000,000 KNEX (90%) |
| **Halving** | Every 3 years |
| **Tail Emission** | 0.1% annual (perpetual) |

---

## SPVT: Stake-Proportional Validation Tiers

Security scales with transaction value:

| Tier | TX Range | Required Stake | Validators |
|------|----------|----------------|------------|
| 1 | < 1K KNEX | Any | 3 |
| 2 | 1K - 100K | 1,000 KNEX | 5 |
| 3 | 100K - 1M | 100,000 KNEX | 7 |
| 4 | 1M - 10M | 1,000,000 KNEX | 9 |
| 5 | > 10M | Match TX | 11 |

---

## Documentation

| Document | Description |
|----------|-------------|
| [Home](Home.md) | Wiki overview |
| [Getting Started](Getting-Started.md) | Setup guide |
| [Technical Architecture](Technical-Architecture.md) | Protocol deep dive |
| [SPVT System](SPVT-Validation-Tiers.md) | Validation tiers |
| [Tokenomics](Tokenomics.md) | Economic model |
| [AyeDEX Exchange](AyeDEX-Exchange.md) | Merchant exchange |
| [Validator Guide](Validator-Guide.md) | Run a node |
| [API Reference](API-Reference.md) | Developer APIs |

---

## Project Structure

```
KnexWallet/
├── Knex Project/
│   └── node/              # Rust full node (5,600+ lines)
├── KnexDAG/               # Python backend (2,300+ lines)
├── idex-svelte/           # AyeDEX SvelteKit app
├── Knexpay/               # Marketing & wallet UIs
├── iDEX (Ayedex)/         # HTML exchange interface
├── sections/              # Whitepaper (28 sections)
└── wiki/                  # Documentation
```

---

## Development Status

| Component | Progress |
|-----------|----------|
| Rust Node | ████████████████████░ 95% |
| Python Backend | ████████████████░░░░ 80% |
| Whitepaper v4.2 | ███████████████████░ 98% |
| Browser Extension | ██████████████░░░░░░ 70% |
| PWA | ████████████████░░░░ 80% |
| **Overall** | █████████████████░░░ **88%** |

**Current Phase:** Testnet Preparation (Q1 2026)

---

## Contributing

We welcome contributions! See [Contributing.md](Contributing.md) for guidelines.

```bash
# Fork and clone
git clone https://github.com/YOUR_USERNAME/knexcoin.git

# Create a branch
git checkout -b feature/your-feature

# Make changes and submit PR
```

---

## Roadmap

### 2026

- [x] Whitepaper v4.2
- [x] Rust node core
- [x] SPVT implementation
- [x] AyeDEX frontend
- [ ] Testnet launch (Q1)
- [ ] Mainnet launch (Q3)

### 2027

- [ ] Mobile apps (iOS & Android)
- [ ] Hardware wallet support
- [ ] KnexMail public launch
- [ ] 10,000+ merchants

---

## Ecosystem

| Platform | Description | Link |
|----------|-------------|------|
| **KnexCoin** | Main website & documentation | [knexcoin.com](https://knexcoin.com) |
| **KnexPay** | Wallet, POS & payment gateway | [knexpay.com](https://knexpay.com) |
| **KnexMail** | Web3 encrypted decentralized mail | [knexmail.com](https://knexmail.com) |
| **AyeDEX** | Merchant-driven exchange | [ayedex.com](https://ayedex.com) |
| **GitHub** | Source code & development | [github.com/knexcoin](https://github.com/knexcoin) |

---

## KnexMail — Web3 Encrypted Mail

**KnexMail** is a decentralized, end-to-end encrypted messaging platform built on the KnexCoin network.

### Features

- **🔐 End-to-End Encryption** — Messages encrypted with post-quantum cryptography
- **📭 Decentralized Storage** — No central servers; messages stored on the DAG
- **💰 Payment Attachments** — Send KNEX directly within messages
- **🆔 Address-Based Identity** — Your `knex_` address is your inbox
- **🚫 No Spam** — Micro-PoW requirement prevents spam attacks
- **🌍 Censorship Resistant** — No central authority can block messages

---

## License

This project is open source. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <strong>KnexCoin</strong> — Building the infrastructure layer for global commerce
</p>

<p align="center">
  Made with ❤️ by the KnexCoin Team
</p>
