# API Reference

Complete reference for KnexCoin's REST and RPC APIs.

---

## Overview

KnexCoin provides two API layers:

| API | Port | Purpose |
|-----|------|---------|
| **Flask API** | 8080 | High-level operations (Python) |
| **Node API** | 7076 | Low-level RPC (Rust) |

---

## Flask API (KnexDAG)

Base URL: `http://localhost:8080/api`

### Account Operations

#### Register Account

Creates a new account on the network.

```http
POST /api/register
Content-Type: application/json
```

**Request:**
```json
{
  "public_key": "3abc...xyz",
  "mode": "custodial"
}
```

**Response:**
```json
{
  "success": true,
  "address": "knex_3abc...xyz",
  "mode": "custodial"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `public_key` | string | Ed25519 public key (hex) |
| `mode` | string | `custodial` or `self-custody` |

---

#### Get Balance

Returns account balance and pending amounts.

```http
GET /api/balance/{address}
```

**Response:**
```json
{
  "address": "knex_3abc...xyz",
  "balance": "1000000000",
  "pending": "50000000",
  "block_count": 42
}
```

| Field | Type | Description |
|-------|------|-------------|
| `balance` | string | Current balance (raw units) |
| `pending` | string | Unclaimed incoming (raw units) |
| `block_count` | integer | Number of blocks in chain |

---

### Transaction Operations

#### Send

Creates a send transaction.

```http
POST /api/send
Content-Type: application/json
```

**Request:**
```json
{
  "from": "knex_3abc...xyz",
  "to": "knex_1def...uvw",
  "amount": "100000000",
  "signature": "abc123...",
  "work": "0000000012345678"
}
```

**Response:**
```json
{
  "success": true,
  "hash": "block_hash_here",
  "pending_for": "knex_1def...uvw"
}
```

---

#### Receive

Claims pending balance.

```http
POST /api/receive
Content-Type: application/json
```

**Request:**
```json
{
  "account": "knex_1def...uvw",
  "source_hash": "send_block_hash",
  "signature": "abc123...",
  "work": "0000000012345678"
}
```

**Response:**
```json
{
  "success": true,
  "hash": "receive_block_hash",
  "new_balance": "1100000000"
}
```

---

#### Get Pending

Lists pending receivables for an account.

```http
GET /api/pending/{address}
```

**Response:**
```json
{
  "address": "knex_1def...uvw",
  "pending": [
    {
      "hash": "send_block_hash_1",
      "amount": "50000000",
      "from": "knex_3abc...xyz"
    },
    {
      "hash": "send_block_hash_2",
      "amount": "25000000",
      "from": "knex_9ghi...rst"
    }
  ]
}
```

---

### Faucet (Testnet Only)

#### Request Testnet KNEX

```http
POST /api/faucet
Content-Type: application/json
```

**Request:**
```json
{
  "address": "knex_3abc...xyz"
}
```

**Response:**
```json
{
  "success": true,
  "amount": "10000000000",
  "hash": "faucet_send_hash"
}
```

---

### Block Operations

#### Get Block

```http
GET /api/block/{hash}
```

**Response:**
```json
{
  "hash": "block_hash",
  "type": "send",
  "account": "knex_3abc...xyz",
  "previous": "previous_hash",
  "representative": "knex_rep...xyz",
  "balance": "900000000",
  "link": "knex_1def...uvw",
  "signature": "abc123...",
  "work": "0000000012345678",
  "height": 42,
  "timestamp": 1706745600
}
```

---

#### Get Account History

```http
GET /api/history/{address}?count=10&offset=0
```

**Response:**
```json
{
  "address": "knex_3abc...xyz",
  "history": [
    {
      "hash": "block_hash_1",
      "type": "send",
      "amount": "100000000",
      "timestamp": 1706745600
    },
    {
      "hash": "block_hash_2",
      "type": "receive",
      "amount": "50000000",
      "timestamp": 1706742000
    }
  ]
}
```

---

## Node API (Rust)

Base URL: `http://localhost:7076`

### Account Info

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "account_info",
  "account": "knex_3abc...xyz"
}
```

**Response:**
```json
{
  "frontier": "latest_block_hash",
  "balance": "1000000000",
  "block_count": "42",
  "representative": "knex_rep...xyz",
  "modified_timestamp": "1706745600"
}
```

---

### Block Info

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "block_info",
  "hash": "block_hash"
}
```

**Response:**
```json
{
  "block_account": "knex_3abc...xyz",
  "amount": "100000000",
  "balance": "900000000",
  "height": "42",
  "confirmed": "true",
  "contents": {
    "type": "send",
    "previous": "prev_hash",
    "link": "destination",
    "representative": "rep_address",
    "signature": "sig...",
    "work": "0000000012345678"
  }
}
```

---

### Process Block

Submit a pre-constructed block.

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "process",
  "block": {
    "type": "send",
    "account": "knex_3abc...xyz",
    "previous": "frontier_hash",
    "representative": "knex_rep...xyz",
    "balance": "900000000",
    "link": "knex_1def...uvw",
    "signature": "abc123...",
    "work": "0000000012345678"
  }
}
```

**Response:**
```json
{
  "hash": "new_block_hash"
}
```

---

### Work Generate

Generate proof-of-work for spam prevention.

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "work_generate",
  "hash": "frontier_hash",
  "difficulty": "fffffff800000000"
}
```

**Response:**
```json
{
  "work": "0000000012345678",
  "difficulty": "fffffffa12345678",
  "multiplier": "1.5"
}
```

---

### Validator Operations

#### Validator Info

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "validator_info",
  "account": "knex_validator...xyz"
}
```

**Response:**
```json
{
  "stake": "10000000000000",
  "is_active": true,
  "reputation": 95,
  "uptime_seconds": 2592000,
  "validations_count": 12345,
  "failures_count": 2
}
```

---

#### Validators List

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "validators",
  "count": 100
}
```

**Response:**
```json
{
  "validators": [
    {
      "account": "knex_val1...xyz",
      "stake": "1000000000000000",
      "reputation": 98
    },
    {
      "account": "knex_val2...xyz",
      "stake": "500000000000000",
      "reputation": 95
    }
  ]
}
```

---

### Network Operations

#### Peers

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "peers"
}
```

**Response:**
```json
{
  "peers": {
    "[::ffff:192.168.1.1]:26656": "3",
    "[::ffff:10.0.0.1]:26656": "2"
  }
}
```

---

#### Version

```http
POST /
Content-Type: application/json
```

**Request:**
```json
{
  "action": "version"
}
```

**Response:**
```json
{
  "rpc_version": "1",
  "node_vendor": "KnexCoin Node",
  "protocol_version": "20",
  "build_info": "release-1.0.0"
}
```

---

## Error Handling

### Error Response Format

```json
{
  "error": "error_code",
  "message": "Human readable description"
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| `invalid_account` | Malformed address |
| `account_not_found` | Account doesn't exist |
| `insufficient_balance` | Not enough funds |
| `invalid_signature` | Signature verification failed |
| `invalid_work` | PoW below threshold |
| `gap_previous` | Previous block doesn't match frontier |
| `fork` | Conflicting block detected |

---

## Rate Limiting

| Endpoint | Limit |
|----------|-------|
| `/api/*` | 100 req/min |
| `/api/faucet` | 1 req/hour |
| Node RPC | 1000 req/min |

---

## WebSocket API

### Connect

```javascript
const ws = new WebSocket('ws://localhost:7077');
```

### Subscribe to Account

```json
{
  "action": "subscribe",
  "topic": "account",
  "accounts": ["knex_3abc...xyz"]
}
```

### Receive Updates

```json
{
  "topic": "account",
  "account": "knex_3abc...xyz",
  "block": {
    "hash": "new_block_hash",
    "type": "receive",
    "amount": "50000000"
  }
}
```

---

## SDKs

### JavaScript/TypeScript

```typescript
import { KnexClient } from '@knexcoin/sdk';

const client = new KnexClient('http://localhost:7076');

// Get balance
const info = await client.accountInfo('knex_3abc...xyz');
console.log(info.balance);

// Send transaction
const result = await client.send({
  from: wallet,
  to: 'knex_1def...uvw',
  amount: '100000000'
});
```

### Python

```python
from knexcoin import KnexClient

client = KnexClient('http://localhost:7076')

# Get balance
info = client.account_info('knex_3abc...xyz')
print(info['balance'])

# Send transaction
result = client.send(
    from_account=wallet,
    to='knex_1def...uvw',
    amount='100000000'
)
```

---

## Further Reading

- [Getting Started](Getting-Started)
- [Technical Architecture](Technical-Architecture)
- [Validator Guide](Validator-Guide)
