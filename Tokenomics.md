# Tokenomics

KnexCoin (KNEX) implements a carefully designed economic model with fixed supply, predictable emission, and long-term sustainability.

---

## Supply Overview

| Parameter | Value |
|-----------|-------|
| **Maximum Supply** | 310,000,000 KNEX |
| **Genesis Allocation** | 21,000,000 KNEX (6.8%) |
| **KnexMail Rewards** | 50,000,000 KNEX (16.1%) |
| **PoB Emission** | 239,000,000 KNEX (77.1%) |
| **Smallest Unit** | 0.0000001 KNEX (1 nano) |
| **Divisibility** | 7 decimal places |

---

## Supply Distribution

```
Total Supply: 310,000,000 KNEX

┌─────────────────────────────────────────────────────────────────┐
│████████████████████████████████████████████████████████████████│ 77.1%
│                    Proof-of-Bandwidth Emission                  │
│                        239,000,000 KNEX                         │
└─────────────────────────────────────────────────────────────────┘
┌──────────────┐
│█████████████ │ 16.1%
│  KnexMail    │
│  Rewards     │
│     50M      │
└──────────────┘
┌───────┐
│██████ │ 6.8%
│Genesis│
│ 21M   │
└───────┘
```

### Genesis Allocation

| Category | Amount | Percentage | Vesting |
|----------|--------|------------|---------|
| Core Development | 10,500,000 | 3.4% | 4-year linear |
| Ecosystem Fund | 6,300,000 | 2.0% | Project grants |
| Early Validators | 2,100,000 | 0.7% | Bootstrap rewards |
| Reserve | 2,100,000 | 0.7% | Emergency fund |

### KnexMail Reward Allocation

| Category | Amount | Percentage | Purpose |
|----------|--------|------------|---------|
| **GENESIS 100** | 1,000,000 | 0.3% | First 100 users @ 10,000 KNEX each |
| **Dynamic Rewards** | 49,000,000 | 15.8% | Auto-adjusting signup bonuses for 500M users |
| **Vesting Pool** | Embedded | N/A | 75% of rewards >100 KNEX vest over 90 days |

**When we get this right. Not if.** The entire crypto community - estimated at 500 million people worldwide - will be onboard before the end of the year. This 50M KNEX allocation (16.1% of total supply) is designed to capture the full global crypto market through viral network effects.

---

## Emission Schedule

### Halving Model

KnexCoin uses a **3-year halving cycle** with eventual transition to perpetual tail emission.

```
Annual Emission vs Time

     │
 31M │████
     │████
     │████
 16M │    ████
     │    ████
  8M │        ████
     │        ████
  4M │            ████
     │                ████
  2M │                    ████
     │                        ████████████ (Tail: 0.1%)
     └─────────────────────────────────────────────────
      Y1   Y3   Y6   Y9   Y12  Y15  Y18  Y21  Y24  Y27+
```

### Cumulative Supply

| Year | Cumulative PoB | Total Supply | % of Max |
|------|----------------|--------------|----------|
| 0 | 0 | 21,000,000 | 10% |
| 3 | 94,500,000 | 115,500,000 | 55% |
| 6 | 141,750,000 | 162,750,000 | 77.5% |
| 9 | 165,375,000 | 186,375,000 | 88.75% |
| 12 | 177,187,500 | 198,187,500 | 94.4% |
| 15 | 183,093,750 | 204,093,750 | 97.2% |
| 20 | 186,000,000 | 207,000,000 | 98.6% |
| 30+ | 189,000,000 + tail | ~210,000,000 | ~100% |

### Halving Formula

```
Block Reward = Initial Reward × (0.5 ^ Halving Period)

Where:
- Initial Reward = 31.5M KNEX / year
- Halving Period = floor(years / 3)
```

---

## Tail Emission

After 10 halvings (~30 years), the network transitions to **perpetual 0.1% annual tail emission**.

### Purpose

1. **Validator Incentives** - Ensures rewards never reach zero
2. **Lost Coin Replacement** - Compensates for permanently lost keys
3. **Economic Activity** - Maintains minimal inflation pressure

### Calculation

```
Annual Tail Emission = Current Supply × 0.001

At 210M supply: ~210,000 KNEX per year
```

---

## Reward Distribution

### Validator Rewards

Validators earn rewards proportional to their contribution:

```rust
Reward = BaseReward × StakeWeight × BandwidthWeight × UptimeWeight

Where:
- StakeWeight = validator_stake / total_stake
- BandwidthWeight = validator_bandwidth / total_bandwidth
- UptimeWeight = uptime_seconds / period_seconds
```

### Reward Decay Function

As circulating supply approaches maximum:

```
R = BaseReward × (1 - S/Cap)^BDC

Where:
- S = Current circulating supply
- Cap = Maximum supply (210M)
- BDC = Block Decay Constant
```

This ensures rewards decrease smoothly as supply grows.

---

## Economic Mechanisms

### Zero Transaction Fees

KnexCoin transactions have **zero fees**. Network security comes from:
- Validator stake requirements
- PoW spam prevention
- Bandwidth contribution incentives

### Stake Lockups

| Action | Lock Period |
|--------|-------------|
| Become validator | None (instant) |
| Increase stake | None |
| Decrease stake | 7 days |
| Exit validator | 14 days |

### Slashing Burns

Slashed tokens are **burned**, permanently reducing supply:

| Violation | Burn Rate |
|-----------|-----------|
| Bandwidth spoofing | 100% |
| Collusion | 75% |
| Attestation fraud | 50% |

---

## Price Discovery

### Merchant-Driven Model

KnexCoin uses a unique **merchant-driven price discovery** mechanism instead of exchange listings.

```
Merchant 1: Coffee $4.99 = 0.5 KNEX    → Implied: $9.98/KNEX
Merchant 2: Laptop $999 = 100 KNEX     → Implied: $9.99/KNEX
Merchant 3: Gas $45 = 4.5 KNEX         → Implied: $10.00/KNEX

Network Average: ~$9.99/KNEX
```

### Benefits

1. **No manipulation** - Price from real commerce, not speculation
2. **Organic adoption** - Value tied to utility
3. **Stability** - Merchants smooth price volatility
4. **Fair access** - No exchange gatekeepers

### Acquiring KNEX

| Method | Description |
|--------|-------------|
| **Merchant** | Accept KNEX for goods/services |
| **Validator** | Earn PoB rewards |
| **P2P Trade** | Direct transfers |
| **Services** | Provide services for KNEX |

---

## Supply Comparison

| Cryptocurrency | Max Supply | Emission |
|----------------|------------|----------|
| **KnexCoin** | 310M | 3-year halving + 0.1% tail + rewards |
| Bitcoin | 21M | 4-year halving |
| Ethereum | Unlimited | Variable (post-merge) |
| Nano | 133M | Fully distributed |
| Monero | ~18.4M | Perpetual 0.6% tail |

---

## Token Utility

### Primary Uses

1. **Medium of Exchange** - Purchase goods and services
2. **Validator Stake** - Secure the network
3. **Governance** - Vote on protocol changes
4. **Bandwidth Credits** - Priority transaction processing

### Value Proposition

```
KNEX Value = f(Merchant Adoption, Network Security, Transaction Volume)
```

The more merchants accept KNEX, the more utility it provides, creating a positive feedback loop.

---

## Economic Security

### Attack Economics

For an attacker to profit, they must:
1. Acquire sufficient stake (market impact)
2. Overcome SPVT requirements
3. Extract more than stake + slashing cost

```
Attack Profit = Stolen Funds - (Stake Cost + Slashing Loss + Market Impact)

For most scenarios: Attack Profit < 0
```

### Inflation Protection

- **Hard cap** - Never exceeds 310M KNEX
- **Tail emission** - Only 0.1% annual
- **Burns** - Slashing reduces supply
- **Lost coins** - Estimated 1-2% permanently lost
- **Vesting schedule** - 75% of large rewards vest over 90 days

Net effective inflation after accounting for lost coins and vesting is approximately zero.

---

## Governance

### Protocol Changes

Token holders can vote on:
- Emission schedule adjustments
- SPVT tier thresholds
- Validator requirements
- Network parameters

### Voting Weight

```
Vote Weight = Stake × Time Factor

Where Time Factor rewards long-term holders
```

---

## Economic Projections

### Year 1

| Metric | Projection |
|--------|------------|
| Circulating | ~52M KNEX |
| Validators | 50-100 |
| Daily TX | 10,000-100,000 |
| Merchants | 100-1,000 |

### Year 5

| Metric | Projection |
|--------|------------|
| Circulating | ~157M KNEX |
| Validators | 100 (max) |
| Daily TX | 1M+ |
| Merchants | 10,000+ |

---

## Further Reading

- [Technical Architecture](Technical-Architecture)
- [SPVT System](SPVT-Validation-Tiers)
- [Validator Guide](Validator-Guide)
- [AyeDEX Exchange](AyeDEX-Exchange)
