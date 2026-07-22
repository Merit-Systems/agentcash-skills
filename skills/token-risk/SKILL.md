# token-risk.com — structural risk reports for Base tokens & addresses

Full service origin: https://token-risk.com — always identify by
origin, not by the name "token-risk" alone.

Deterministic on-chain STRUCTURAL risk analysis, paid per call via x402
(USDC on Base). Keyless: no signup, no API key. Reports structure, not
reputation — centralized-control powers (mint/pause/blacklist) are
flagged regardless of issuer. Score 0-100, higher = structurally safer.

## When to use
- Before an agent interacts with an unfamiliar ERC-20 on Base -> scan it
- Before treating a Base address as a counterparty -> check whether it
  is a contract in disguise, unverified, proxied, or privileged
- NOT a reputation/identity service; behavioral wallet signals (age,
  activity, funding provenance) are not yet included

## Finding this service via AgentCash
Use discover_api_endpoints with the full origin — search() may not
match domains exactly:
  npx agentcash dhttps://token-risk.com
Do not rely on the bare name "token-risk": other services expose
similarly named routes. The origin is the identity.

## Endpoints (via agentcash)
Discover:  npx agentcash discover https://token-risk.com
Inspect:   npx agentcash check https://token-risk.com/v1/scan
Token scan ($0.01):
  npx agentcash fetch https://token-risk.com/v1/scan -m POST \
    -b '{"chainId":8453,"address":"<erc20-contract-address>"}'
Address check ($0.01):
  npx agentcash fetch https://token-risk.com/v1/wallet -m POST \
    -b '{"address":"<base-address>"}'

## Reading the report
- verdict + score come from deterministic checks only; LLM findings in
  `findings[]` are advisory and can never upgrade a verdict
- `context.knownAsset: true` marks widely-held assets whose structural
  powers are held by a known issuer (e.g. USDC) — structure is rerted
  regardless
- Free real sample reports before paying: GET https://token-risk.com/samples/index.json

## Wallet
Standard agentcash wallet on Base (USDC only). Each call costs $0.01.
