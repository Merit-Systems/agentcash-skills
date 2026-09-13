---
name: pathmint
description: |
  Before paying an x402 402, ping the resource live vs dead (ghost listing).
  Optional pay-clearance when stake is at least $0.50.

  USE FOR:
  - Checking whether an x402 URL is live or a ghost bazaar listing
  - Verify-before-pay when holding Payment-Required
  - CLEAR / CAUTION / ABORT on a payTo when about to send >= $0.50 USDC
  - Finding reachable x402 sellers for a job (not the graveyard)

  TRIGGERS:
  - "ghost 402", "is this endpoint live", "verify before pay"
  - "ping x402", "dead listing", "bazaar ghost"
  - "pay-clearance", "should I pay this 402"
  - "live x402 seller", "don't shop the graveyard"

  Use agentcash.fetch against pathmint.edge.report. Identify by origin, not
  the word "ping" alone. agentcash.check_endpoint_schema is free schema/price
  only — it does not tell you live vs ghost.
mcp:
  - agentcash
metadata:
  version: 1
---

# Pathmint — ping x402 live vs ghost

Full service origin: https://pathmint.edge.report — always identify by origin.

Ping probes the URL you are about to pay and returns `live` or `dead`. A 402
from a ghost listing still 402s; `check_endpoint_schema` will still show a
price. Ping is the live/dead bit. Not a token-risk check. Not a bazaar dump.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Live vs ghost | `GET https://pathmint.edge.report/v1/ping?url=` | $0.01 | Before any 402 settle |
| Pay-clearance | `POST https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile` | $0.25 | Stake ≥ $0.50 |
| Live sellers | `POST https://pathmint.edge.report/v1/paths/x402-live-seller/compile` | $0.35 | Need a reachable seller |
| Manifests | `GET https://pathmint.edge.report/v1/paths` | free | List paths |
| Pack | `GET https://pathmint.edge.report/v1/agent-tools.json` | free | Hardcoded tools |

See [rules/when-to-use.md](rules/when-to-use.md) for skip vs pay vs abort.

## Finding this service via AgentCash

```mcp
agentcash.discover_api_endpoints(url="https://pathmint.edge.report")
```

Do not bazaar-search for "ghost x402" / "is this endpoint live". Use the origin.

## Ping (default, $0.01)

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/ping?url=https://EXAMPLE/paid"
)
```

Optional: append `&payTo=0x…` from the 402.

**Returns:** JSON with `live` / `dead`, `http_status`, `host`, `cached`.

- `live` → you may pay that resource (or run clearance if stake ≥ $0.50)
- `dead` → do not settle USDC to that listing

**Free fallback (weaker):** `agentcash.check_endpoint_schema(url="<resource>")`
reports schema and price. It does **not** replace ping. Skip ping only for
dust you already trust ($0.001) where 1¢ is not worth it.

## Pay-clearance (stake ≥ $0.50, $0.25)

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile",
  method="POST",
  body={"payTo": "0xPAYTO", "amountUsd": 0.50}
)
```

**Returns:** `CLEAR` / `CAUTION` / `ABORT`.

- `ABORT` → do not pay that merchant
- Do not buy a $0.25 compile to protect a $0.001 dust payment — use ping

## Live sellers (stake ≥ $0.70, $0.35)

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/paths/x402-live-seller/compile",
  method="POST",
  body={"job": "wallet", "amountUsd": 5}
)
```

Up to 3 reachable sellers with real outside USDC — not bazaar ghosts.

## Workflows

### About to pay a 402

- [ ] (Optional) Check balance: `agentcash.get_balance()`
- [ ] Ping the resource URL ($0.01)
- [ ] If `dead`, stop
- [ ] If `live` and stake < $0.50, pay the original resource
- [ ] If `live` and stake ≥ $0.50, clearance then follow the verdict

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/ping?url=https://EXAMPLE/paid"
)
```

## Cost Optimization

- Ping once per resource; responses may be cached
- Skip clearance below $0.50 stake
- Never invent Pathmint paths — discover if unsure
- Merchant Truth (`x402-merchant-truth`) is a scaffold; do not buy it
