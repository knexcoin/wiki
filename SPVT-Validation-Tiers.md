# SPVT: Stake-Proportional Validation Tiers

The **Stake-Proportional Validation Tiers (SPVT)** system is KnexCoin's innovative approach to transaction security, ensuring validators have economic stake proportional to the transaction values they validate.

---

## Overview

Traditional PoS systems use uniform validation regardless of transaction size. SPVT scales security requirements with transaction value, making large-value attacks economically irrational.

```
Transaction Value → Required Stake → Validator Count → Consensus Threshold
```

---

## Tier Structure

| Tier | Name | TX Range | Required Stake | Min Validators | Consensus |
|------|------|----------|----------------|----------------|-----------|
| 1 | **Micro** | < 1,000 KNEX | Any stake | 3 | 67% |
| 2 | **Standard** | 1K - 100K KNEX | 1,000 KNEX | 5 | 67% |
| 3 | **Large** | 100K - 1M KNEX | 100,000 KNEX | 7 | 67% |
| 4 | **Whale** | 1M - 10M KNEX | 1,000,000 KNEX | 9 | 67% |
| 5 | **Mega** | > 10M KNEX | Match TX amount | 11 | 75% |

---

## How It Works

### Tier Selection

```
                    Transaction Submitted
                            │
                            ▼
                ┌───────────────────────┐
                │  Determine TX Value   │
                └───────────┬───────────┘
                            │
            ┌───────────────┼───────────────┐
            │               │               │
            ▼               ▼               ▼
    ┌───────────┐   ┌───────────┐   ┌───────────┐
    │ < 1K KNEX │   │ 1K - 100K │   │  > 100K   │
    │  Tier 1   │   │  Tier 2   │   │ Tier 3+   │
    └─────┬─────┘   └─────┬─────┘   └─────┬─────┘
          │               │               │
          ▼               ▼               ▼
    3 validators    5 validators    7+ validators
    Any stake       1K+ stake       100K+ stake
```

### Validator Selection

For each tier, validators are selected based on:

1. **Stake eligibility** - Must meet tier minimum
2. **Reputation score** - Higher = more likely selected
3. **Bandwidth capacity** - Proven through PoB
4. **Geographic diversity** - Prevent regional attacks

```rust
fn select_validators(tier: Tier, tx: &Transaction) -> Vec<Validator> {
    let eligible = validators
        .filter(|v| v.stake >= tier.required_stake)
        .filter(|v| v.is_active)
        .filter(|v| v.reputation >= MIN_REPUTATION);

    weighted_random_select(eligible, tier.min_validators)
}
```

---

## Security Analysis

### Attack Cost Calculation

To successfully attack a transaction, an adversary must:
1. Control enough validators to reach consensus threshold
2. Each controlled validator must have sufficient stake

```
Attack Cost = Required Stake × Colluding Validators

Tier 1:  Any × 2        = Minimal
Tier 2:  1K × 4         = 4,000 KNEX
Tier 3:  100K × 5       = 500,000 KNEX
Tier 4:  1M × 6         = 6,000,000 KNEX
Tier 5:  10M × 9        = 90,000,000 KNEX
```

### Economic Security Property

For any transaction value `V`:

```
Attack Cost > V × Safety Multiplier
```

Where Safety Multiplier ensures attacking is never profitable.

### Tier 5 (Mega) Special Case

For transactions exceeding 10M KNEX:
- Each validator must stake **at least the transaction amount**
- 11 validators required with 75% consensus
- Attack requires 9 validators each holding 10M+ KNEX
- **Attack cost: 90M+ KNEX for a 10M transaction**

This makes large-value attacks economically irrational.

---

## Consensus Process

### Attestation Flow

```
1. Transaction broadcast to network
              │
              ▼
2. Tier determined from value
              │
              ▼
3. Eligible validators selected
              │
              ▼
4. Each validator independently verifies:
   - Signature validity
   - Balance sufficiency
   - No double-spend
   - Block hash correctness
              │
              ▼
5. Validators sign attestation
              │
              ▼
6. Attestations aggregated
              │
              ▼
7. Consensus threshold reached?
       │              │
      YES            NO
       │              │
       ▼              ▼
  Confirmed      Wait/Retry
```

### Attestation Data

```rust
pub struct Attestation {
    pub block_hash: [u8; 32],
    pub validator: [u8; 32],
    pub signature: [u8; 64],
    pub timestamp: u64,
    pub tier: u8,
}
```

---

## Implementation Details

### Tier Eligibility Check

```rust
pub fn get_eligible_validators(&self, amount: u128) -> Vec<&ValidatorInfo> {
    let tier = self.get_tier_for_amount(amount);
    let required_stake = self.get_required_stake(tier, amount);

    self.validators
        .values()
        .filter(|v| v.stake >= required_stake && v.is_active)
        .collect()
}
```

### Tier Determination

```rust
pub fn get_tier_for_amount(&self, amount: u128) -> u8 {
    match amount {
        0..=999                    => 1,  // Micro
        1_000..=99_999             => 2,  // Standard
        100_000..=999_999          => 3,  // Large
        1_000_000..=9_999_999      => 4,  // Whale
        _                          => 5,  // Mega
    }
}
```

### Consensus Threshold

```rust
pub fn get_consensus_threshold(&self, tier: u8) -> f64 {
    match tier {
        1..=4 => 0.67,  // 67% for Tiers 1-4
        5     => 0.75,  // 75% for Mega tier
        _     => 0.67,
    }
}
```

---

## Slashing Conditions

Validators who misbehave face stake slashing:

| Violation | Penalty | Detection |
|-----------|---------|-----------|
| **Bandwidth Spoofing** | 100% | Failed challenge verification |
| **Collusion** | 75% | Multiple validators signing conflicting blocks |
| **Attestation Fraud** | 50% | Signing invalid transactions |
| **Downtime** | Reputation decay | Missed validation rounds |

### Slashing Process

```
1. Violation detected by network
              │
              ▼
2. Evidence submitted on-chain
              │
              ▼
3. Challenge period (24 hours)
              │
              ▼
4. If no valid defense:
   - Stake slashed
   - Validator deactivated
   - Slashed funds burned
```

---

## Tier Comparison

### Security vs. Speed Tradeoff

| Tier | Confirmation Speed | Security Level | Use Case |
|------|-------------------|----------------|----------|
| 1 | ~100ms | Basic | Coffee, small purchases |
| 2 | ~200ms | Standard | Daily transactions |
| 3 | ~500ms | High | Business payments |
| 4 | ~1s | Very High | Large transfers |
| 5 | ~2s | Maximum | Institutional settlements |

### Validator Participation

```
Tier 1: ████████████████████ 95% of validators eligible
Tier 2: ████████████████░░░░ 80% of validators eligible
Tier 3: ██████████░░░░░░░░░░ 50% of validators eligible
Tier 4: ████░░░░░░░░░░░░░░░░ 20% of validators eligible
Tier 5: ██░░░░░░░░░░░░░░░░░░ 10% of validators eligible
```

---

## Design Rationale

### Why Tiered Validation?

1. **Efficiency** - Small transactions don't need heavyweight consensus
2. **Security** - Large transactions get proportional protection
3. **Scalability** - Network can handle high throughput for micro-transactions
4. **Economics** - Attack costs scale with potential gains

### Why These Thresholds?

| Threshold | Reasoning |
|-----------|-----------|
| 1,000 KNEX | Daily spending limit for most users |
| 100,000 KNEX | Small business transaction range |
| 1,000,000 KNEX | Large business / institutional |
| 10,000,000 KNEX | Major institutional settlements |

### Why 67%/75% Consensus?

- **67% (2/3 + 1):** Byzantine fault tolerance standard
- **75% for Tier 5:** Extra security margin for highest-value transactions

---

## Monitoring & Analytics

### Tier Distribution Dashboard

The network tracks:
- Transactions per tier
- Average confirmation time per tier
- Validator participation rates
- Slashing events

### Health Metrics

```
Tier 1 Validators Active: 95/100
Tier 2 Validators Active: 80/100
Tier 3 Validators Active: 50/100
Tier 4 Validators Active: 20/100
Tier 5 Validators Active: 10/100

24h Transactions by Tier:
Tier 1: 1,234,567 (89%)
Tier 2:   123,456 (9%)
Tier 3:    12,345 (1%)
Tier 4:     1,234 (<1%)
Tier 5:       123 (<1%)
```

---

## Further Reading

- [Technical Architecture](Technical-Architecture)
- [Validator Guide](Validator-Guide)
- [Tokenomics](Tokenomics)
- [Security Model](Security)
