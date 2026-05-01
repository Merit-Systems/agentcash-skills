---
name: keeperhub
description: |
  Web3 workflow automation via x402 payments. Read DeFi yield/risk data, check on-chain account health, and send tips on Base.

  USE FOR:
  - Comparing USDC yield rates across DeFi protocols (Aave vs Compound)
  - Checking the health factor and risk level of an Aave v3 account
  - Sending micropayments to an Ethereum address (microtip)
  - Triggering KeeperHub workflows that wrap multi-step on-chain reads or RPC calls
  - Discovering which workflows are available at runtime

  TRIGGERS:
  - "yield rates", "compare aave compound", "best stablecoin yield"
  - "health factor", "aave health", "liquidation risk"
  - "tip", "send a small payment", "microtip"
  - "on-chain check", "defi risk", "web3 workflow"
  - "keeperhub", "keeper workflow"

  ALWAYS use `agentcash.fetch` for app.keeperhub.com endpoints.

  IMPORTANT: Workflows are addressed by slug under `/api/mcp/workflows/{slug}/call`. Always discover available workflows first — the catalog and pricing change. Paid workflows return HTTP 402 on the first call; agentcash handles payment automatically and replays.
metadata:
  version: 1
---

# KeeperHub — Web3 Workflow Automation

Run DeFi reads, on-chain health checks, and tipping primitives via x402 payments at `https://app.keeperhub.com`. USDC on Base mainnet. No account, no API key.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Discover workflows (list) | `https://app.keeperhub.com/api/mcp/workflows` | Free |
| Discover workflows (full schema) | `https://app.keeperhub.com/openapi.json` | Free |
| Compare USDC yield (Aave vs Compound) | `https://app.keeperhub.com/api/mcp/workflows/usdc-yield-rates-aave-vs-compound/call` | $0.01 |
| Check Aave v3 account health | `https://app.keeperhub.com/api/mcp/workflows/aave-v3-health-check/call` | $0.01 |
| Send a micropayment (microtip) | `https://app.keeperhub.com/api/mcp/workflows/microtip/call` | $0.01 |

Network: Base mainnet. All paid workflows settle on success only — failed responses (4xx/5xx) cost nothing.

All workflow calls return JSON with `executionId`, `status`, and an `output` payload. The shape of `output.result` is per-workflow — check the OpenAPI spec or the response itself.

## Discover what's available

```mcp
agentcash.fetch(url="https://app.keeperhub.com/api/mcp/workflows")
```

Returns the full list of listed workflows: slug, description, price, and which ones are free. Use this before guessing — KeeperHub adds and removes workflows over time.

For full per-workflow request/response schemas:

```mcp
agentcash.fetch(url="https://app.keeperhub.com/openapi.json")
```

Or use the agentcash discovery shorthand:

```mcp
agentcash.discover_api_endpoints(url="https://app.keeperhub.com")
```

## Compare USDC yield rates (Aave vs Compound)

```mcp
agentcash.fetch(
  url="https://app.keeperhub.com/api/mcp/workflows/usdc-yield-rates-aave-vs-compound/call",
  method="POST",
  body={}
)
```

Response shape:

```json
{
  "executionId": "exec_...",
  "status": "success",
  "output": {
    "result": {
      "rates": [
        {"protocol": "aave-v3", "supplyApy": 0.0412, "borrowApy": 0.0521},
        {"protocol": "compound-v3", "supplyApy": 0.0398, "borrowApy": 0.0487}
      ],
      "bestRate": 0.0412,
      "bestProtocol": "aave-v3"
    }
  }
}
```

No inputs required — the workflow reads current rates from chain. Use this when the user wants a real-time yield comparison.

## Check Aave v3 account health

```mcp
agentcash.fetch(
  url="https://app.keeperhub.com/api/mcp/workflows/aave-v3-health-check/call",
  method="POST",
  body={"address": "0x..."}
)
```

Response shape:

```json
{
  "executionId": "exec_...",
  "status": "success",
  "output": {
    "result": {
      "healthFactor": 1.84,
      "totalCollateralUSD": 12500.0,
      "totalDebtUSD": 5200.0,
      "riskLevel": "moderate"
    }
  }
}
```

`address` is the Aave v3 borrower's wallet address (any chain Aave v3 supports). `riskLevel` is one of `"safe" | "moderate" | "risky" | "liquidatable"`. Use this before the user takes a position or to monitor an existing one.

## Send a micropayment (microtip)

A tipping primitive that emits a small USDC transfer on Base. The exact request body shape isn't yet declared in `/openapi.json`, so check it before calling:

```mcp
agentcash.check_endpoint_schema(url="https://app.keeperhub.com/api/mcp/workflows/microtip/call")
```

Then call it via:

```mcp
agentcash.fetch(
  url="https://app.keeperhub.com/api/mcp/workflows/microtip/call",
  method="POST",
  body={ ... see check output ... }
)
```

Some tipping/write-type workflows return unsigned calldata in `output.result` for the caller to sign and broadcast separately; others settle the on-chain action via the same x402 payment. The OpenAPI 402 example for the workflow shows which mode it uses.

## Failure modes

- `402` on first call — expected. agentcash handles payment automatically and replays.
- `400` — schema error on request body. Check the per-workflow `requestBody` field at `/openapi.json`.
- `404` — workflow slug doesn't exist. Re-list `/api/mcp/workflows` for the current catalog.
- `5xx` from the workflow — upstream RPC or DeFi protocol issue. No settlement on failure. Safe to retry.

## When to use KeeperHub vs other origins

- **Use KeeperHub** for live DeFi reads, on-chain account checks, and on-chain payments wrapped as named workflows.
- **Use stableenrich** for off-chain people/company research, web search, and scraping.
- **Use stablesocial** for social-platform data.

## Learn more

- Homepage: https://keeperhub.com
- Docs: https://docs.keeperhub.com
- OpenAPI spec: https://app.keeperhub.com/openapi.json
