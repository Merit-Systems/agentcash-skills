# When to ping vs check vs clearance

## Decision tree

```
About to pay an x402 402?
|
+-- Dust you already trust ($0.001)? --> Optional: skip ping
|
+-- Need schema/price only? --> agentcash check <url> (free)
|                               Does NOT tell you live vs ghost
|
+-- Need live vs dead? --> Pathmint GET /v1/ping?url= ($0.01)
|   |
|   +-- dead --> do not pay
|   |
|   +-- live and stake < $0.50 --> pay the original resource
|   |
|   +-- live and stake >= $0.50 --> POST pay-clearance ($0.25)
|       |
|       +-- ABORT --> do not pay
|       +-- CAUTION --> pay only if you accept the reasons
|       +-- CLEAR --> pay the original resource
|
+-- Need a seller for a job, not a specific URL?
    --> POST live-seller ($0.35) when stake >= $0.70
```

## What ping is not

| Need | Do not use Pathmint ping | Use |
|------|--------------------------|-----|
| Token honeypot / ERC-20 risk | ping | a token-risk origin |
| Web search | ping | web-research skill |
| Price/schema of a 402 | ping | `agentcash check` (free) |

## Origin

Always call `https://pathmint.edge.report`. Other services expose similarly
named "ping" or "trust" routes. The origin is the identity.
