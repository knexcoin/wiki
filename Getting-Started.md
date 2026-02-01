# Getting Started with KnexCoin

This guide will help you get started with KnexCoin, whether you're a user, merchant, or developer.

---

## For Users

### Creating a Wallet

#### Option 1: Browser Extension (Recommended)
1. Install the KnexWallet browser extension
2. Click "Create New Wallet"
3. Securely store your 24-word recovery phrase
4. Your wallet is ready to receive KNEX

#### Option 2: Progressive Web App
1. Visit [knexpay.com](https://knexpay.com)
2. Click "Add to Home Screen" on mobile
3. Create or import a wallet
4. Enable biometric authentication for security

#### Option 3: Key Generator
1. Visit the [Key Generator](https://knexpay.com/keygen.html)
2. Generate an Ed25519 keypair
3. Download your keys securely
4. Optional: Generate a vanity address

### Receiving KNEX

KNEX can be acquired through:
- **Merchant services** - Accept KNEX for goods/services
- **Validator rewards** - Provide bandwidth to the network
- **P2P transfers** - Receive from other users

> **Note:** KnexCoin is not listed on traditional exchanges. Price discovery happens through merchant consensus.

---

## For Merchants

### Why Accept KNEX?

- **Zero fees** - No transaction fees or chargebacks
- **Instant settlement** - Transactions confirm in seconds
- **Price control** - Set your own KNEX acceptance rate
- **Verified status** - Earn trust badges for your business

### Integration Steps

1. **Register as a Merchant**
   - Complete KYC verification
   - Receive your merchant credentials
   - Get your verified badge

2. **Set Your Prices**
   - Display dual pricing (USD/KNEX)
   - Your rates contribute to network price discovery
   - Update rates based on market conditions

3. **Accept Payments**
   - Generate payment QR codes
   - Accept NFC tap payments
   - View real-time transaction feed

4. **Access Analytics**
   - Track sales volume
   - Compare rates vs network average
   - Export transaction reports

---

## For Developers

### Local Development Setup

#### Prerequisites
- Rust 1.70+ (for node)
- Python 3.10+ (for KnexDAG)
- Node.js 18+ (for frontend)

#### Running the Node

```bash
# Clone the repository
git clone https://github.com/knexcoin/knexcoin.git
cd knexcoin

# Build the Rust node
cd "Knex Project/node"
cargo build --release

# Run the node
./target/release/knex-node
```

#### Running KnexDAG Backend

```bash
cd KnexDAG

# Install dependencies
pip install -r requirements.txt

# Start the Flask API
python -m src.api
```

#### Running AyeDEX Frontend

```bash
cd idex-svelte

# Install dependencies
npm install

# Start development server
npm run dev
```

### Network Ports

| Port | Protocol | Service |
|------|----------|---------|
| 26656 | TCP | P2P Networking |
| 26657 | TCP | RPC Interface |
| 9090 | TCP | gRPC |
| 26660 | UDP | Fast Relay |
| 7076 | HTTP | Rust Node API |
| 8080 | HTTP | Flask API |

---

## Account Structure

KnexCoin uses a **block-lattice** architecture where each account maintains its own chain:

```
Account: knex_3abc...xyz
├── Block 0 (OPEN)     - Initial balance from first receive
├── Block 1 (RECEIVE)  - Claimed 100 KNEX from sender
├── Block 2 (SEND)     - Sent 50 KNEX to recipient
├── Block 3 (RECEIVE)  - Claimed 25 KNEX from another sender
└── Block 4 (CHANGE)   - Changed representative validator
```

### Block Types

| Type | Purpose |
|------|---------|
| `OPEN` | First block, establishes account |
| `SEND` | Creates pending amount for recipient |
| `RECEIVE` | Claims pending balance |
| `CHANGE` | Updates representative validator |
| `BANDWIDTH` | Records bandwidth proof (validators) |

---

## Address Format

KnexCoin addresses use the `knex_` prefix followed by a 60-character Base32 encoding:

```
knex_3t6k35gi95xu6tergt6p69ck76ogmitsa8mnijtpxm9fkcm736xtoncuohr3
```

- First character after prefix: account type indicator
- Remaining characters: public key encoding
- Final 8 characters: checksum

---

## Next Steps

- [Technical Architecture](Technical-Architecture) - Understand the protocol
- [SPVT System](SPVT-Validation-Tiers) - Learn about validation tiers
- [API Reference](API-Reference) - Build integrations
- [Validator Guide](Validator-Guide) - Run a node

---

## Need Help?

- **GitHub Issues:** Report bugs and request features
- **Documentation:** Full whitepaper in `/sections/`
- **Community:** Join the discussion

---

*Happy building with KnexCoin!*
