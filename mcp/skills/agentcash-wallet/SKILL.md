---
name: agentcash-wallet
description: |
  Manage your agentcash wallet and call any x402-protected API with automatic payment. No API keys, no subscriptions — just a funded wallet (USDC on Base).

  USE FOR:
  - Checking wallet balance before API calls
  - Redeeming invite codes for free credits
  - Getting deposit address for USDC
  - Discovering endpoints and pricing on any x402-protected origin
  - Making paid API requests via agentcash.fetch
  - Troubleshooting payment failures

  TRIGGERS:
  - "balance", "wallet", "funds", "credits", "x402"
  - "redeem", "invite code", "promo code"
  - "deposit", "add funds", "top up"
  - "discover", "endpoints", "what APIs", "pricing"
  - "insufficient balance", "payment failed"
---

# agentcash Wallet & Paid APIs

Call any x402-protected API with automatic payment. Payment is the authentication — no API keys or subscriptions needed.

## Setup

If the agentcash MCP is not yet installed, see [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Wallet Management

Your wallet is auto-created on first use and stored at `~/.agentcash/wallet.json`.

### Check Balance

```mcp
agentcash.get_wallet_info()
```

Returns wallet address, USDC balance, and deposit link. Always check before expensive operations.

### Redeem Invite Code

```mcp
agentcash.redeem_invite(code="YOUR_CODE")
```

One-time use per code. Credits added instantly. Run `get_wallet_info` after to verify.

### Deposit USDC

1. Get your wallet address via `agentcash.get_wallet_info`
2. Send USDC on **Base network** (eip155:8453) to that address
3. Or use the deposit UI: `https://x402scan.com/mcp/deposit/<wallet-address>`

**Important**: Only Base network USDC. Other networks or tokens will be lost.

## Calling Paid APIs

### 1. Discover endpoints

```mcp
agentcash.discover_api_endpoints(url="https://stableenrich.dev")
```

Returns all endpoints, pricing, and usage instructions. **Read the `instructions` field** — it has critical endpoint-specific guidance.

### 2. Check schema (optional)

```mcp
agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/apollo/people-search")
```

Returns full request/response JSON schemas and pricing for a specific endpoint.

### 3. Make a paid request

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-search",
  method="POST",
  body={"person_titles": ["CEO"], "person_locations": ["San Francisco"]}
)
```

Payment is automatic: sends request, gets 402 challenge, signs USDC payment, retries with credential, returns result. Payments settle only on success (2xx) — failed requests cost nothing.

## Available Services

| Origin | Service | What it does |
|---|---|---|
| `https://stableenrich.dev` | StableEnrich | Research APIs: Apollo (people/org), Exa (web search), Firecrawl (scraping), Grok (X/Twitter), Google Maps, Clado (LinkedIn), Serper (news/shopping), WhitePages, Reddit, Hunter (email verification), Influencer enrichment |
| `https://stableupload.dev` | StableUpload | Pay-per-upload file hosting. 10MB/$0.02, 100MB/$0.20, 1GB/$2.00. 6-month TTL |
| `https://stablestudio.dev` | StableStudio | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan |
| `https://stablesocial.dev` | StableSocial | Social media data: TikTok, Instagram, X/Twitter, Facebook, Reddit, LinkedIn. $0.06/call, async two-step |
| `https://stableemail.dev` | StableEmail | Send emails ($0.02), forwarding inboxes ($1/mo), custom subdomains ($5) |
| `https://stablephone.dev` | StablePhone | AI phone calls ($0.54), phone numbers ($20), top-ups ($15) |
| `https://stablejobs.dev` | StableJobs | Job search via Coresignal |

Run `agentcash.discover_api_endpoints(url="<origin>")` on any origin to see its full endpoint catalog.

## Quick Reference

| Task | Tool |
|------|------|
| Check balance | `agentcash.get_wallet_info` |
| Redeem code | `agentcash.redeem_invite(code="...")` |
| Discover endpoints | `agentcash.discover_api_endpoints(url="...")` |
| Check pricing/schema | `agentcash.check_endpoint_schema(url="...")` |
| Paid POST request | `agentcash.fetch(url="...", method="POST", body={...})` |
| Paid GET request | `agentcash.fetch(url="...")` |
| Authenticated GET (no payment) | `agentcash.fetch_with_auth(url="...")` |

## Tips

- Always discover first — the `instructions` field has critical endpoint-specific patterns and required parameters.
- Payments settle only on success (2xx) — failed requests cost nothing.
- Use `check_endpoint_schema` when unsure about request/response format.
- Independent `agentcash.fetch` calls can run in parallel for better throughput.
- Network: Base (eip155:8453), Currency: USDC.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Reinstall MCP, restart IDE |
| "Insufficient balance" | Check balance, deposit USDC or redeem invite code |
| "Payment failed" | Transient error — retry the request |
| "Invalid invite code" | Code already used or doesn't exist |
| Balance not updating | Wait for Base network confirmation (~2 sec) |
