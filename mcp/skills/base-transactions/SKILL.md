---
name: base-transactions
description: |
  Decide when to send a Base mainnet transaction and whether a sent one is safe to act on, via x402.

  USE FOR:
  - Choosing max fee and priority fee before submitting a Base transaction
  - Deciding whether to submit now or wait for a cheaper window
  - Estimating the USD cost of an ETH transfer, USDC transfer, or x402 settlement
  - Checking whether a submitted tx is safe (L1-attested) or finalized (L1-finalized)
  - Deciding when a payment is irreversible enough to ship goods or credit an account

  TRIGGERS:
  - "gas", "gas price", "base fee", "priority fee", "EIP-1559"
  - "should I send now", "wait for cheaper", "how much will this cost"
  - "is it confirmed", "is it final", "safe to act on", "reorg"
  - "before every send", "after submitting"

  Use agentcash.fetch for these endpoints. $0.01 each, GET, no API key.
mcp:
  - agentcash
metadata:
  version: 2
---

# Base Transaction Decisions

Two calls that bracket sending a transaction on Base mainnet: what fee to use
before you submit, and whether the result is irreversible after you do.

Both are computed live from Base RPC blocks. Neither is a modeled prediction —
finality is read from the node's own `safe` and `finalized` block tags, and the
fee is standard EIP-1559 arithmetic over the current base fee.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Returns |
|------|----------|-------|---------|
| Pre-send fee decision | `https://x402-mcp.onrender.com/base/tx-decision` | $0.01 | submit/wait, max fee + priority fee (gwei), USD cost |
| Post-send finality | `https://x402-mcp.onrender.com/base/finality-check` | $0.01 | not_found / pending / unsafe / safe / finalized |
| Full network briefing | `https://x402-mcp.onrender.com/swarm/products/d22bbf5f3c4b4666a6f80980c7bc7c50/purchase` | $0.05 | congestion, trend, ETH price, settle-now verdict |
| Free preview of the briefing | `https://x402-mcp.onrender.com/pulse` | free | same shape, no payment |

See [rules/when-to-use.md](rules/when-to-use.md) for choosing between them.

## Before you send: tx-decision

```bash
agentcash.fetch(url="https://x402-mcp.onrender.com/base/tx-decision?gas=usdc&urgency=flexible")
```

**Parameters:**
- `gas` — `eth` (21000), `usdc` (55000), `erc20`, `x402`, or an integer of gas units. Default `usdc`.
- `urgency` — `now` (price it, always submit), `soon` (time-sensitive), `flexible` (wait for a cheap window). Default `flexible`.

**Returns:**

```json
{
  "submit": true,
  "verdict": "SETTLE_NOW",
  "why": "...",
  "fee": {
    "max_fee_per_gas_gwei": 0.011,
    "max_priority_fee_per_gas_gwei": 0.001,
    "current_base_fee_gwei": 0.005,
    "next_base_fee_gwei": 0.005
  },
  "estimated_cost": { "gas": 55000, "eth": 3.3e-7, "usd": 0.00062 },
  "recheck_in_s": 30,
  "as_of_block": 49116340,
  "as_of": "2026-08-02T04:00:00+00:00"
}
```

`max_fee_per_gas_gwei` is `2 × base_fee + tip`, the standard headroom for one
base-fee doubling. `estimated_cost` charges only `base + tip`, which is what you
actually pay. When `submit` is false, `recheck_in_s` is how long to wait before
asking again — it is a backoff, never a prediction that the price will drop.

## After you send: finality-check

```bash
agentcash.fetch(url="https://x402-mcp.onrender.com/base/finality-check?tx=0xTX_HASH")
```

**Parameters:**
- `tx` — 0x-prefixed 32-byte transaction hash (required)

**Returns:**

```json
{
  "tx": "0x7fe54ef1...",
  "included": true,
  "status": "finalized",
  "tx_block": 49106777,
  "confirmations": 9563,
  "safe_block": 49116306,
  "finalized_block": 49115873,
  "latest_block": 49116340,
  "why": "Block 49106777 is at or behind the chain's 'finalized' block ...",
  "as_of": "2026-08-02T04:00:00+00:00"
}
```

**Status ladder:**

| status | Meaning | Reasonable action |
|--------|---------|-------------------|
| `not_found` | No such hash on Base mainnet | Do not assume dropped; it may not have propagated |
| `pending` | Known, not yet in a block | Wait |
| `unsafe` | In a block, sequencer-confirmed only | Fine for UX, not for releasing value |
| `safe` | Block attested on L1 | Safe for most commerce |
| `finalized` | Block finalized on L1 | Irreversible; safe to ship goods or credit an account |

## Paying

Both endpoints are x402: an unpaid GET returns `402` with a `PAYMENT-REQUIRED`
header carrying the challenge. `agentcash.fetch` handles the signing and retry.

- Network: Base mainnet (`eip155:8453`)
- Asset: USDC (`0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`)
- Amount: `10000` atomic units = $0.01
- Settlement is gasless for the buyer (EIP-3009), so you need USDC, not ETH

## What can go wrong

- **Facilitator 502 mid-settle** — transient. No funds move and nothing is
  delivered; retry the same request. There is nothing to reconcile.
- **Staleness** — responses carry `as_of_block` and `as_of`. Base blocks land
  every ~2s and the server caches its RPC snapshot for ~4s; treat anything more
  than a few blocks old as history, not advice.
- **422 with no charge** — a malformed parameter. Nothing was charged.

Full operator documentation, including failure modes:
<https://x402-mcp.onrender.com/llms.txt>
