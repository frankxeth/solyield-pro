---
name: solyield-pro
version: 1.0.0
description: Autonomous DeFi Yield Optimizer on Solana using OKX Onchain OS
author: frankxeth
chain: solana
category: defi
tags: [yield, optimizer, defi, solana, apy, rebalancing, autonomous]
requires:
  - okx-defi-invest
  - okx-dex-swap
  - okx-market-data
  - okx-wallet-portfolio
  - okx-security
  - okx-agentic-wallet
min_balance_usd: 10
max_tokens: 4096
language: id, en
---

# SolYield Pro — Autonomous DeFi Yield Optimizer

## 🎯 Purpose

SolYield Pro helps users maximize DeFi yield on Solana by autonomously scanning protocols, comparing APY, and rebalancing positions when better opportunities arise — all with built-in risk controls.

---

## 🔑 Trigger Phrases

This skill activates when the user says any of the following:

### English
- `"Find the best yield on Solana"`
- `"Optimize my DeFi yield"`
- `"Start yield farming on Solana"`
- `"Show me the highest APY pools"`
- `"Rebalance my yield positions"`
- `"How much am I earning from DeFi?"`
- `"Start SolYield Pro"`
- `"Activate yield optimizer"`

### Bahasa Indonesia
- `"Cari yield terbaik di Solana"`
- `"Optimalkan yield DeFi saya"`
- `"Tampilkan pool APY tertinggi"`
- `"Mulai yield farming di Solana"`
- `"Rebalance posisi yield saya"`
- `"Berapa penghasilan DeFi saya?"`

### ❌ Do NOT trigger for:
- General crypto price questions → use `okx-market-data` instead
- Token swaps without yield intent → use `okx-dex-swap` instead
- Portfolio balance checks → use `okx-wallet-portfolio` instead
- Questions about staking on non-Solana chains

---

## 📋 Instructions

### Step 1 — Scan Yield Opportunities
Use `okx-defi-invest` to scan the following Solana protocols:
- Raydium (AMM pools)
- Orca (CLMM pools)
- Marinade Finance (liquid staking)
- Jupiter (aggregated routes)

Fetch current APY, TVL, and 24h volume for each pool.

**Why:** APY data changes frequently. Always fetch fresh data before recommending any position.

**Output format:**
```
Protocol   | Pool        | APY     | TVL      | Risk
-----------|-------------|---------|----------|------
Raydium    | SOL-USDC    | 18.4%   | $45.2M   | Low
Orca       | USDC-USDT   | 12.1%   | $22.1M   | Low
Marinade   | mSOL stake  | 7.2%    | $312M    | Low
```

---

### Step 2 — Risk Assessment
Before recommending any pool, use `okx-security` to verify:
1. Protocol is whitelisted and audited
2. TVL has not dropped >20% in last 24h
3. No recent exploit alerts

**Why:** User funds must only enter safe, verified protocols.

If any check fails → skip that protocol and explain why to the user.

---

### Step 3 — Recommend Optimal Allocation
Calculate Adjusted APY Score:
```
score = base_apy + (volume_24h_bonus × 0.3) - (il_risk_penalty × 0.5)
```

Recommend top 2-3 pools ranked by score.

Always show:
- Expected monthly yield in USD
- Estimated gas fees
- Impermanent loss risk level (Low / Medium / High)

**Example output:**
```
🏆 Top Yield Opportunities (Solana)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Raydium SOL-USDC
   APY: 18.4% | Monthly est: +$18.40 per $1,000
   IL Risk: Low | Gas: ~$0.001
   ✅ Recommended

2. Orca USDC-USDT  
   APY: 12.1% | Monthly est: +$12.10 per $1,000
   IL Risk: Very Low | Gas: ~$0.001
   ✅ Stable option

Proceed with allocation? (yes/no)
```

---

### Step 4 — Execute Position (with confirmation)
Only execute after explicit user confirmation.

For positions > $500: always ask for confirmation even if user said "auto".

Use `okx-dex-swap` for token conversion, then `okx-defi-invest` to enter position.

**Why:** User must retain control over large fund movements.

---

### Step 5 — Monitor & Rebalance
After position entry, monitor every 15 minutes.

**Rebalance trigger:**
```
IF current_pool_apy < best_available_apy - 2%:
    → notify user
    → if auto_rebalance=true: execute swap to better pool
    → if auto_rebalance=false: ask user permission first
```

**Daily summary** (send once per day at user's preferred time):
```
📊 SolYield Pro Daily Report
━━━━━━━━━━━━━━━━━━━━━━━━━━
Portfolio Value : $XXX.XX
Today's Yield   : +$X.XX
Total PnL       : +$XX.XX
Active Pools    : X
Rebalances      : X
━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## ⚠️ Risk Controls

```yaml
max_allocation_per_pool: 30%        # Never put >30% in one pool
stop_loss_il_threshold: 15%         # Exit if impermanent loss > 15%
tvl_drop_alert: 20%                 # Alert if TVL drops > 20% in 24h
daily_loss_limit: 10%               # Pause all activity if portfolio drops > 10%
whitelist_only: true                # Only audited protocols
min_pool_tvl_usd: 1000000           # Minimum $1M TVL pool
confirmation_threshold_usd: 500     # Always confirm for tx > $500
```

---

## 🔧 Error Handling & Fallback

| Error | Action |
|---|---|
| Protocol API timeout | Retry 3x, then skip and use next best pool |
| Insufficient balance | Notify user, suggest minimum deposit amount |
| Slippage > 1% | Abort swap, notify user, suggest retry later |
| Security check fail | Skip protocol, explain reason to user |
| Gas spike > 10× normal | Warn user, ask to proceed or wait |
| All protocols unavailable | Notify user, suggest holding USDC |

---

## 💬 Example Conversations

### Example 1 — Basic scan
```
User: "Cari yield terbaik di Solana"

Agent: Scanning Solana DeFi protocols...

🏆 Top Yield Opportunities ditemukan:
1. Raydium SOL-USDC — APY 18.4%
2. Orca USDC-USDT — APY 12.1%
3. Marinade mSOL — APY 7.2%

Berapa budget yang ingin Anda alokasikan?
```

### Example 2 — With budget
```
User: "Mulai dengan $200"

Agent: Untuk $200, estimasi hasil bulanan:
• Raydium SOL-USDC: +$3.07/bulan
• Gas fee: ~$0.002

Konfirmasi untuk melanjutkan? (ya/tidak)
```

### Example 3 — Rebalance alert
```
Agent: 📢 Rebalance Alert!
Orca USDC-USDT APY turun ke 8.1%
Raydium SOL-USDC sekarang 21.3%

Delta: +13.2% — rekomendasi pindah posisi.
Auto-rebalance? (ya/tidak)
```

### Example 4 — Error fallback
```
Agent: ⚠️ Raydium API timeout.
Menggunakan Orca sebagai alternatif.
APY: 12.1% | TVL: $22.1M | Status: ✅ Aman

Lanjutkan dengan Orca? (ya/tidak)
```

---

## 📁 Folder Structure

```
.agents/skills/solyield-pro/
├── SKILL.md          ← This file (main skill definition)
└── references/
    ├── protocols.md  ← Supported protocol list
    └── risk-guide.md ← Risk parameter documentation
```

---

## 📊 Performance Targets

| Metric | Target |
|---|---|
| APY vs benchmark | +2% above SOL staking baseline |
| Max drawdown | < 10% |
| Rebalance frequency | 1-3x per week |
| Gas efficiency | < 0.1% of position value |
| Uptime | 99%+ autonomous operation |
