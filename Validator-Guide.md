# Validator Guide

This guide covers everything you need to know about running a KnexCoin validator node, from requirements to optimization.

---

## Overview

Validators secure the KnexCoin network by:
- Verifying transaction signatures and balances
- Attesting to block validity
- Providing bandwidth to the network
- Participating in consensus

In return, validators earn KNEX rewards proportional to their stake, bandwidth contribution, and uptime.

---

## Requirements

### Minimum Requirements

| Resource | Minimum | Recommended |
|----------|---------|-------------|
| **Stake** | 10,000 KNEX | 100,000+ KNEX |
| **CPU** | 4 cores | 8+ cores |
| **RAM** | 8 GB | 16+ GB |
| **Storage** | 100 GB SSD | 500+ GB NVMe |
| **Bandwidth** | 100 Mbps | 1 Gbps |
| **Uptime** | 95% | 99.9% |

### Network Ports

| Port | Protocol | Purpose | Required |
|------|----------|---------|----------|
| 26656 | TCP | P2P networking | Yes |
| 26657 | TCP | RPC interface | Optional |
| 9090 | TCP | gRPC | Optional |
| 26660 | UDP | Fast relay | Yes |
| 7076 | HTTP | Node API | Optional |

---

## Setup

### 1. Install Dependencies

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install -y build-essential curl git

# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

### 2. Build the Node

```bash
# Clone repository
git clone https://github.com/knexcoin/knexcoin.git
cd knexcoin

# Build release binary
cd "Knex Project/node"
cargo build --release

# Binary location
./target/release/knex-node --version
```

### 3. Configure Node

Create configuration file at `~/.knex/config.toml`:

```toml
[node]
data_dir = "~/.knex/data"
log_level = "info"

[network]
listen_addr = "0.0.0.0:26656"
external_addr = "YOUR_PUBLIC_IP:26656"
seeds = [
  "seed1.knexcoin.io:26656",
  "seed2.knexcoin.io:26656"
]
max_peers = 50

[rpc]
enabled = true
listen_addr = "127.0.0.1:26657"

[validator]
enabled = true
stake_address = "knex_YOUR_VALIDATOR_ADDRESS"
bandwidth_target_mbps = 100
```

### 4. Generate Validator Keys

```bash
# Generate new keypair
./knex-node keygen --output ~/.knex/validator.key

# View public key
./knex-node show-address --keyfile ~/.knex/validator.key
```

### 5. Fund Validator Account

Transfer at least 10,000 KNEX to your validator address to meet minimum stake requirements.

### 6. Start Node

```bash
# Start in foreground
./knex-node start --config ~/.knex/config.toml

# Or run as systemd service
sudo systemctl start knex-validator
```

---

## Systemd Service

Create `/etc/systemd/system/knex-validator.service`:

```ini
[Unit]
Description=KnexCoin Validator Node
After=network.target

[Service]
Type=simple
User=knex
ExecStart=/home/knex/knexcoin/target/release/knex-node start --config /home/knex/.knex/config.toml
Restart=always
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable knex-validator
sudo systemctl start knex-validator

# Check status
sudo systemctl status knex-validator

# View logs
sudo journalctl -u knex-validator -f
```

---

## Registration

### Automatic Registration

When your node has sufficient balance (≥10,000 KNEX), it automatically registers as a validator:

```
INFO  validator registered: stake=10000 KNEX, active=true
```

### Manual Registration

```bash
./knex-node validator register \
  --stake 10000 \
  --bandwidth 100 \
  --keyfile ~/.knex/validator.key
```

### Check Registration Status

```bash
./knex-node validator status
```

Output:
```
Validator Status
================
Address: knex_3abc...xyz
Stake: 10,000 KNEX
Status: Active
Reputation: 95/100
Uptime: 99.5%
Validations: 12,345
Failures: 2
Tier Eligibility: 1, 2
```

---

## Staking

### Increasing Stake

```bash
# Add more stake (instant)
./knex-node validator stake add --amount 5000
```

### Decreasing Stake

```bash
# Reduce stake (7-day unlock period)
./knex-node validator stake reduce --amount 5000
```

### Exiting

```bash
# Exit validator role (14-day unlock period)
./knex-node validator exit
```

### Stake Tiers

| Stake Amount | Tier Eligibility |
|--------------|------------------|
| 10,000+ KNEX | Tier 1, 2 |
| 100,000+ KNEX | Tier 1, 2, 3 |
| 1,000,000+ KNEX | Tier 1, 2, 3, 4 |
| 10,000,000+ KNEX | All tiers |

---

## Rewards

### Reward Formula

```
Daily Reward = BaseReward × StakeWeight × BandwidthWeight × UptimeWeight

Where:
- BaseReward = Daily emission / Active validators
- StakeWeight = Your stake / Total stake
- BandwidthWeight = Your bandwidth / Average bandwidth
- UptimeWeight = Your uptime % / 100
```

### Estimated Returns

| Stake | Bandwidth | Uptime | Est. Daily Reward |
|-------|-----------|--------|-------------------|
| 10,000 KNEX | 100 Mbps | 95% | ~5-10 KNEX |
| 100,000 KNEX | 500 Mbps | 99% | ~50-100 KNEX |
| 1,000,000 KNEX | 1 Gbps | 99.9% | ~500-1000 KNEX |

*Estimates vary based on network conditions and total validator count.*

### Reward Distribution

Rewards are distributed every epoch (1 hour):

```
INFO  epoch 12345 rewards distributed: 8.5 KNEX
```

---

## Monitoring

### Built-in Metrics

```bash
# Check node status
./knex-node status

# View peer connections
./knex-node peers

# Check sync status
./knex-node sync-status
```

### Prometheus Metrics

Enable in config:

```toml
[metrics]
prometheus_enabled = true
prometheus_addr = "127.0.0.1:9090"
```

Key metrics:
- `knex_validator_uptime_seconds`
- `knex_validator_validations_total`
- `knex_validator_stake_amount`
- `knex_validator_reputation`
- `knex_bandwidth_mbps`

### Alerts

Set up alerts for:
- Node offline (uptime < 95%)
- Peer count low (< 5 peers)
- Missed validations
- Bandwidth below threshold

---

## Bandwidth Proofs

### How PoB Works

1. Network issues random challenge
2. Validator must relay data packets
3. Latency and throughput measured
4. Results recorded on-chain

### Configuration

```toml
[bandwidth]
target_mbps = 100          # Target bandwidth
test_interval = 300        # Seconds between tests
min_peers_for_test = 3     # Minimum peers required
```

### Optimizing Bandwidth

- Use quality hosting provider
- Enable QoS for validator traffic
- Monitor network utilization
- Avoid shared bandwidth

---

## Security

### Key Management

```bash
# Backup validator key
cp ~/.knex/validator.key /secure/backup/

# Use hardware security module (HSM) if available
./knex-node validator set-hsm --device /dev/hsm0
```

### Firewall Rules

```bash
# Allow P2P
sudo ufw allow 26656/tcp
sudo ufw allow 26660/udp

# Allow RPC (localhost only)
# Do NOT expose to public

# Enable firewall
sudo ufw enable
```

### Best Practices

1. **Use dedicated server** - Don't run other services
2. **Keep software updated** - Apply security patches
3. **Monitor logs** - Watch for anomalies
4. **Secure keys** - Use HSM or encrypted storage
5. **Geographic diversity** - Run in different regions

---

## Slashing

### Conditions

| Violation | Penalty | Detection |
|-----------|---------|-----------|
| Bandwidth spoofing | 100% stake | Failed PoB verification |
| Collusion | 75% stake | Conflicting attestations |
| Attestation fraud | 50% stake | Invalid signature |
| Extended downtime | Reputation decay | Missed epochs |

### Avoiding Slashing

- Ensure stable network connection
- Monitor bandwidth capacity
- Never run multiple validators with same key
- Keep node software updated

### Slashing Recovery

If slashed, you can:
1. Investigate the cause
2. Fix the issue
3. Wait for unlock period
4. Re-register with new stake

---

## Troubleshooting

### Node Won't Sync

```bash
# Check peer connections
./knex-node peers

# Clear database and resync
./knex-node unsafe-reset-all
./knex-node start
```

### Low Reputation

- Check uptime percentage
- Verify bandwidth capacity
- Review validation failure logs
- Ensure clock is synchronized

### Connection Issues

```bash
# Test connectivity
nc -zv seed1.knexcoin.io 26656

# Check firewall
sudo ufw status

# Verify external IP
curl ifconfig.me
```

### High Memory Usage

```toml
# Tune in config
[database]
cache_size_mb = 512        # Reduce if needed
max_open_files = 256
```

---

## FAQ

**Q: When do I start earning rewards?**
A: Immediately after registration and your first validation.

**Q: Can I run multiple validators?**
A: Yes, but each needs unique stake and infrastructure.

**Q: What happens if my node goes offline?**
A: Reputation decays, reducing reward share. Extended offline results in deactivation.

**Q: How do I upgrade my node?**
A: Build new version, stop service, replace binary, restart.

**Q: Can I change my stake address?**
A: Yes, use the CHANGE block type to update.

---

## Further Reading

- [Technical Architecture](Technical-Architecture)
- [SPVT System](SPVT-Validation-Tiers)
- [Tokenomics](Tokenomics)
- [API Reference](API-Reference)
