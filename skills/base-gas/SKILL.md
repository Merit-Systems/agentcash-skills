---
name: base-gas
description: |
  Live and historical Base mainnet gas prices via x402, including cross-chain comparison and cheapest-hour scheduling.

  USE FOR:
  - Checking current gas prices on Base before sending a transaction
  - Estimating transaction cost for a transfer, swap, NFT mint, or contract deploy
  - Deciding whether gas is currently cheap or expensive
  - Comparing gas costs across Base, OP Mainnet, Arbitrum, and Ethereum
  - Finding the cheapest hour of day to transact on Base
  - Budgeting gas spend for on-chain agent workloads

  TRIGGERS:
  - "gas price", "gas fees", "gwei", "base fee", "priority fee"
  - "how much to send", "transaction cost", "cost to swap", "cost to mint"
  - "is gas cheap right now", "should I transact now or wait"
  - "compare gas", "cheapest chain", "which L2 is cheaper"
  - "best time to transact", "cheapest hour", "schedule transaction"

  Call `GET /health` first (free) before paying for history endpoints. Use `npx agentcash@latest fetch` for the paid routes.
metadata:
  version: 1
---

# Base Gas

Live and historical Base mainnet gas data through x402-protected endpoints, served from `https://base-gas-x402-production.up.railway.app`.

All values are read directly from the chain. Fees are returned in gwei.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Returns |
|------|----------|-------|---------|
| Current gas | `GET /gas` | $0.005 | Base fee, low/med/high priority tiers, cost estimate |
| Compare chains | `GET /gas/compare` | $0.01 | Base, OP, Arbitrum, Ethereum ranked cheapest first |
| Historical trend | `GET /gas/history` | $0.012 | Time series, min/max/avg/median, cheap-or-expensive verdict |
| Cheapest hours | `GET /gas/cheapest-window` | $0.02 | Hourly averages ranked, cheapest hour, savings percent |
| Data coverage | `GET /health` | **free** | How much history has been collected so far |

See [rules/choosing-an-endpoint.md](rules/choosing-an-endpoint.md) for which route answers which question.

## Current Gas

```bash
npx agentcash@latest fetch https://base-gas-x402-production.up.railway.app/gas
```

**Parameters:**
- `gasLimit` (optional) - Gas units to price the estimate against. Defaults to 21000.

Price the cost of a Uniswap swap instead of a plain transfer:

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas?gasLimit=180000"
```

**Common gas limits:**

| Operation | gasLimit |
|-----------|----------|
| ETH transfer | 21000 |
| ERC-20 transfer | 65000 |
| NFT mint | 85000 |
| Uniswap swap | 180000 |
| Contract deploy | 1500000 |

**Returns:** `baseFeePerGas`, `priorityFeePerGas` (low/medium/high), `gasPrice`, `estimatedTransferCost` (gwei and ETH), `blockNumber`, `fetchedAt`.

## Compare Chains

```bash
npx agentcash@latest fetch https://base-gas-x402-production.up.railway.app/gas/compare
```

**Parameters:**
- `gasLimit` (optional) - Gas units to price each chain against. Defaults to 21000.

**Returns:** `chains` ranked cheapest first, `cheapest`, `baseRank`, `baseVsEthereum` (a plain-language multiplier), and `unavailable` for any chain whose RPC did not respond.

Chains that fail are reported rather than failing the whole request, so a partial comparison still returns useful data.

## Historical Trend

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas/history?hours=24"
```

**Parameters:**
- `hours` (optional) - Lookback window, 1 to 168. Defaults to 24.

**Returns:** `samples` time series, `summary` (min, max, avg, median), `currentGasPrice`, `verdict`, `verdictNote`, and `spreadPercent`.

The `verdict` field is the useful part: `cheap`, `normal`, or `expensive` relative to the requested window, or `flat` when the window's spread is under 1%. A raw RPC call cannot answer this, because it has no history to compare against.

`flat` is common on Base and is a real answer: gas is not moving, so there is nothing to wait for. Quote `verdictNote` rather than reinterpreting it.

## Cheapest Hours

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas/cheapest-window?hours=168"
```

**Parameters:**
- `hours` (optional) - Lookback window used to compute hourly averages, 1 to 168. Defaults to 168 (7 days).

**Returns:** `hasDailyCycle`, a plain-language `recommendation`, `savingsPercent`, and `hourlyAverages` bucketed by hour of day in UTC and ranked cheapest first, plus `cheapestHourUtc` and `priciestHourUtc`.

Check `hasDailyCycle` before using the ranking. When it is `false` the hours do not meaningfully differ and timing saves nothing; report `recommendation` instead of naming a cheapest hour.

## Check Coverage First (free)

Both history endpoints depend on samples collected over time. Check coverage before paying:

```bash
curl -s https://base-gas-x402-production.up.railway.app/health
```

**Returns:** `samples`, `hoursCovered`, `oldestSample`, `newestSample`, `sampleIntervalSeconds`, `retentionHours`.

If `hoursCovered` is smaller than the window you need, the answer will be thin. Every paid response also carries a `coverage` object so the depth of the underlying data is always visible.

## Workflows

### Decide whether to transact now

- [ ] Check the trend and verdict: `GET /gas/history?hours=24`
- [ ] If `verdict` is `flat`, proceed and tell the user there is nothing to wait for
- [ ] If `cheap`, proceed
- [ ] If `expensive`, check whether waiting actually helps: `GET /gas/cheapest-window`

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas/history?hours=24"
```

### Price a specific operation

- [ ] Pick the gasLimit for the operation type
- [ ] Call `/gas` with that gasLimit
- [ ] Read `estimatedTransferCost.eth`

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas?gasLimit=85000"
```

### Pick the cheapest chain

- [ ] (Optional) Check coverage or balance
- [ ] Compare chains at the relevant gasLimit
- [ ] Route the transaction to `cheapest`

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas/compare?gasLimit=180000"
```

### Schedule a batch job

- [ ] Check history coverage: `GET /health` (free)
- [ ] If coverage is at least 24 hours, call `/gas/cheapest-window`
- [ ] If `hasDailyCycle` is `false`, report `recommendation` and run the job whenever
- [ ] If `true`, schedule the job for `cheapestHourUtc` (convert from UTC)

```bash
curl -s https://base-gas-x402-production.up.railway.app/health
```

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas/cheapest-window?hours=168"
```

## Cost Optimization

### Use `/gas` when:
- You only need the current price
- You are pricing a single known operation

### Use `/gas/history` when:
- The question is "is this high or low", not "what is it"
- You need a trend or chart

### Use `/gas/cheapest-window` when:
- The work can wait
- You are scheduling recurring or batch transactions

### Efficient patterns

1. **Check `/health` before history calls.** It is free and tells you whether the data is deep enough to be worth paying for.
2. **One `/gas/compare` beats four `/gas` calls.** It reads all four chains in a single paid request.
3. **Pass `gasLimit` instead of doing the math.** The estimate is returned already priced for the operation you care about.
4. **Do not poll.** Base blocks are two seconds apart but gas moves slowly; sampling more than once a minute spends money for no new information.

## Notes

- Payment settles on Base mainnet (`eip155:8453`) in USDC via x402.
- Prices are published in the endpoint's OpenAPI document at `/openapi.json` and are the source of truth if they ever differ from this table.
- The service is open source: [github.com/memosr/base-gas-x402](https://github.com/memosr/base-gas-x402).
