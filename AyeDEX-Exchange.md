# AyeDEX Exchange

**AyeDEX** (also known as iDEX) is KnexCoin's decentralized merchant exchange platform that facilitates organic price discovery through real-world commerce.

---

## Overview

Unlike traditional cryptocurrency exchanges, AyeDEX doesn't match buy/sell orders. Instead, it aggregates pricing data from merchant transactions to establish a fair market rate.

```
┌─────────────────────────────────────────────────────────────────┐
│                         AyeDEX                                   │
│                                                                  │
│   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐        │
│   │Merchant │   │Merchant │   │Merchant │   │Merchant │        │
│   │   A     │   │    B    │   │    C    │   │    D    │        │
│   │ $10/K   │   │ $9.95/K │   │ $10.05/K│   │ $9.90/K │        │
│   └────┬────┘   └────┬────┘   └────┬────┘   └────┬────┘        │
│        │             │             │             │              │
│        └─────────────┴──────┬──────┴─────────────┘              │
│                             │                                    │
│                      ┌──────▼──────┐                            │
│                      │  Aggregate  │                            │
│                      │   Price     │                            │
│                      │  $9.975/K   │                            │
│                      └─────────────┘                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## Core Features

### Live Transaction Feed

Real-time stream of merchant transactions across the network:

```
┌─────────────────────────────────────────────────────────────┐
│ LIVE TRANSACTION FEED                           ●  LIVE     │
├─────────────────────────────────────────────────────────────┤
│ ☕ Starbucks         Coffee          0.50 KNEX     $4.99    │
│ 🍕 Dominos          Large Pizza      2.00 KNEX    $19.99    │
│ 🛒 Walmart          Groceries       12.50 KNEX   $124.87    │
│ ⛽ Shell            Gas             4.50 KNEX    $44.99     │
│ 💊 CVS              Prescriptions    8.25 KNEX    $82.45    │
│ 🍔 McDonald's       Combo Meal       1.20 KNEX    $11.99    │
└─────────────────────────────────────────────────────────────┘
```

### Price Discovery Engine

Aggregates merchant prices to determine network rate:

| Metric | Value | Change |
|--------|-------|--------|
| Current Price | $9.99/KNEX | +0.5% |
| 24h High | $10.15 | - |
| 24h Low | $9.85 | - |
| 24h Volume | 125,432 KNEX | +12% |
| 24h Trades | 45,678 | +8% |

### Merchant Dashboard

For businesses accepting KNEX:

- **Sales Analytics** - Track revenue in KNEX and USD
- **Rate Comparison** - Your rate vs. network average
- **Transaction History** - Complete payment records
- **Customer Insights** - Spending patterns
- **Verification Status** - Verified merchant badge

---

## Technical Stack

### Frontend Framework

```
AyeDEX
├── SvelteKit v2
├── TypeScript
├── Vite Build Tool
└── TailwindCSS
```

### Core Stores

| Store | Purpose |
|-------|---------|
| `feed.ts` | Live transaction streaming |
| `price.ts` | Price aggregation and tracking |
| `wallet.ts` | KnexWallet integration |
| `knex.ts` | Blockchain RPC client |

### Key Dependencies

```json
{
  "@noble/ed25519": "Cryptographic signatures",
  "@noble/hashes": "Blake2b hashing",
  "svelte": "UI framework",
  "vite": "Build tool"
}
```

---

## Price Discovery Algorithm

### Weighted Average

```typescript
function calculateNetworkPrice(transactions: Transaction[]): number {
  const recentTx = transactions.filter(tx =>
    tx.timestamp > Date.now() - 24 * 60 * 60 * 1000
  );

  let totalVolume = 0;
  let weightedSum = 0;

  for (const tx of recentTx) {
    const impliedPrice = tx.usdAmount / tx.knexAmount;
    const weight = tx.knexAmount; // Volume weighting

    weightedSum += impliedPrice * weight;
    totalVolume += weight;
  }

  return weightedSum / totalVolume;
}
```

### Price Factors

| Factor | Weight | Description |
|--------|--------|-------------|
| Volume | 40% | Higher volume = more influence |
| Recency | 30% | Recent transactions weighted more |
| Merchant Tier | 20% | Verified merchants have more influence |
| Consistency | 10% | Stable pricing rewarded |

---

## Merchant Integration

### Registration Flow

```
1. Apply for merchant account
              │
              ▼
2. Complete KYC verification
              │
              ▼
3. Receive API credentials
              │
              ▼
4. Integrate payment system
              │
              ▼
5. Start accepting KNEX
              │
              ▼
6. Earn verification badge
```

### Payment Flow

```typescript
// Customer pays merchant
const payment = {
  merchant: "knex_merchant123...",
  amount: 2.50,           // KNEX
  usdEquivalent: 24.99,   // USD at merchant's rate
  product: "Large Coffee",
  timestamp: Date.now()
};

// Submit to network
await knexWallet.send(payment);

// Transaction appears in live feed
// Price discovery engine aggregates
```

### Merchant Tiers

| Tier | Requirements | Benefits |
|------|--------------|----------|
| **Basic** | Complete registration | Accept payments |
| **Verified** | KYC + 30 days activity | Blue badge, price influence |
| **Premium** | High volume + 90 days | Priority support, analytics |
| **Enterprise** | Custom integration | Dedicated account manager |

---

## User Interface

### Main Dashboard

```
┌─────────────────────────────────────────────────────────────────┐
│  AyeDEX                                      [Connect Wallet]   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  KNEX/USD                          Volume (24h)                 │
│  ┌─────────────────┐               ┌─────────────────┐          │
│  │    $9.99        │               │   125.4K KNEX   │          │
│  │    ▲ +0.50%     │               │   $1.25M USD    │          │
│  └─────────────────┘               └─────────────────┘          │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    PRICE CHART (24H)                      │   │
│  │     ╭───╮                        ╭─────╮                 │   │
│  │  ╭──╯   ╰──╮    ╭────╮      ╭───╯     ╰───╮             │   │
│  │ ─╯         ╰────╯    ╰──────╯              ╰────         │   │
│  │  9.85                                         10.15      │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  LIVE FEED                         MERCHANTS                    │
│  ┌─────────────────────────┐       ┌─────────────────────┐     │
│  │ ☕ Starbucks    0.50K   │       │ Online: 1,234       │     │
│  │ 🍕 Dominos      2.00K   │       │ TX Today: 45,678    │     │
│  │ 🛒 Walmart     12.50K   │       │ New: 23             │     │
│  │ ⛽ Shell        4.50K   │       │                     │     │
│  └─────────────────────────┘       └─────────────────────┘     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Pages

| Page | Purpose |
|------|---------|
| `/` | Main dashboard with price and feed |
| `/feed` | Dedicated transaction feed |
| `/merchant` | Merchant analytics dashboard |
| `/buy` | Acquire KNEX (merchant listings) |
| `/sell` | Offer goods/services for KNEX |
| `/orderbook` | View merchant price listings |
| `/account` | User account management |
| `/security` | Security settings |

---

## Wallet Integration

### KnexWallet Extension

```typescript
import { wallet } from '$lib/stores/wallet';

// Connect wallet
await wallet.connect();

// Check balance
const balance = await wallet.getBalance();

// Send transaction
await wallet.send({
  to: 'knex_recipient...',
  amount: 10.0
});

// Sign message
const signature = await wallet.sign(message);
```

### Supported Actions

| Action | Description |
|--------|-------------|
| `connect()` | Link browser extension |
| `disconnect()` | Unlink wallet |
| `getBalance()` | Query current balance |
| `send()` | Create send transaction |
| `receive()` | Claim pending funds |
| `sign()` | Sign arbitrary message |

---

## API Endpoints

### Public Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/price` | GET | Current network price |
| `/api/feed` | GET | Recent transactions |
| `/api/merchants` | GET | Merchant directory |
| `/api/stats` | GET | Network statistics |

### Merchant Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/merchant/register` | POST | Register merchant |
| `/api/merchant/verify` | POST | Submit KYC |
| `/api/merchant/payment` | POST | Create payment request |
| `/api/merchant/history` | GET | Transaction history |

### WebSocket

```typescript
// Connect to live feed
const ws = new WebSocket('wss://ayedex.io/ws/feed');

ws.onmessage = (event) => {
  const tx = JSON.parse(event.data);
  console.log(`New TX: ${tx.merchant} - ${tx.amount} KNEX`);
};
```

---

## Security Features

### Transaction Safety

- **Signature verification** - All transactions signed with Ed25519
- **Address validation** - Checksum verification
- **Amount limits** - Configurable daily limits
- **2FA support** - Optional two-factor authentication

### Merchant Verification

- **KYC compliance** - Identity verification required
- **Address verification** - Business location confirmed
- **Activity monitoring** - Anomaly detection
- **Fraud prevention** - Suspicious activity flagging

---

## Development

### Running Locally

```bash
cd idex-svelte

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

### Environment Variables

```env
VITE_KNEX_RPC_URL=http://localhost:7076
VITE_KNEX_WS_URL=ws://localhost:7077
VITE_API_BASE_URL=http://localhost:8080
```

### Project Structure

```
idex-svelte/
├── src/
│   ├── lib/
│   │   ├── stores/
│   │   │   ├── feed.ts       # Transaction feed
│   │   │   ├── price.ts      # Price tracking
│   │   │   ├── wallet.ts     # Wallet integration
│   │   │   └── index.ts      # Store exports
│   │   ├── blockchain/
│   │   │   ├── knex.ts       # RPC client
│   │   │   └── index.ts      # Exports
│   │   └── components/
│   │       ├── Nav.svelte    # Navigation
│   │       └── ...           # UI components
│   └── routes/
│       ├── +page.svelte      # Home/dashboard
│       ├── feed/             # Feed page
│       ├── merchant/         # Merchant dashboard
│       ├── buy/              # Buy interface
│       ├── sell/             # Sell interface
│       └── ...               # Other pages
├── static/                   # Static assets
├── package.json
└── svelte.config.js
```

---

## Roadmap

### Current (v1.0)

- [x] Live transaction feed
- [x] Price discovery engine
- [x] Wallet integration
- [x] Merchant dashboard
- [x] Basic analytics

### Upcoming (v1.1)

- [ ] Mobile-responsive redesign
- [ ] Advanced charting
- [ ] Merchant API v2
- [ ] Multi-language support

### Future (v2.0)

- [ ] P2P marketplace
- [ ] Escrow services
- [ ] Merchant ratings
- [ ] Loyalty programs

---

## Further Reading

- [Getting Started](Getting-Started)
- [Technical Architecture](Technical-Architecture)
- [API Reference](API-Reference)
- [Tokenomics](Tokenomics)
