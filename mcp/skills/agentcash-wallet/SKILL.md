---
name: agentcash-wallet
description: |
  Manage your agentcash wallet and call any x402-protected API with automatic payment. No API keys, no subscriptions — just a funded wallet (USDC on Base or Solana).

  USE FOR:
  - Checking wallet balance before API calls
  - Redeeming invite codes for free credits
  - Getting deposit address for USDC
  - Searching for paid APIs by natural language when you do not know which origin to use (agentcash.search)
  - Discovering endpoints and pricing on any x402-protected origin
  - Making paid API requests via agentcash.fetch
  - Troubleshooting payment failures

  TRIGGERS:
  - "balance", "wallet", "funds", "credits", "x402"
  - "redeem", "invite code", "promo code"
  - "deposit", "add funds", "top up", "paid", "premium"
  - "discover", "endpoints", "what APIs", "pricing"
  - "find an API", "search for a service", "what paid API", "browse APIs"
  - "insufficient balance", "payment failed"
metadata:
  version: 2.1
---

# AgentCash Wallet & Paid APIs

Call any x402-protected API with automatic wallet authentication and payment. No API keys or subscriptions needed.

## Setup

If the agentcash MCP is not yet installed, see [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Wallet Management

Your wallet is auto-created on first use and stored at `~/.agentcash/wallet.json`.

### Check Balance

```mcp
agentcash.get_balance()
```

Returns total USDC balance across supported networks. Use this before paid calls to confirm funds are available.

### Redeem Invite Code

```mcp
agentcash.redeem_invite(code="YOUR_CODE")
```

One-time use per code. Credits added instantly. Run `agentcash.get_balance()` after to verify.

### Deposit USDC

1. Call `agentcash.list_accounts()` to get per-network wallet addresses and deposit links
2. Send USDC on **Base network** (eip155:8453) or **Solana** (solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp) to the matching address
3. Or open the returned deposit link for the selected network

**Important**: Only Base or Solana network USDC. Other networks or tokens will be lost.

## Calling Paid APIs

### 1. Search for a service (when the origin is unknown)

```mcp
agentcash.search(query="send physical mail")
```

Use **search** when you need a paid API but do not know which origin or endpoint fits the task. It returns matching origins with endpoints and pricing (the top match often includes schema so you can call **fetch** next).

**Skip search** when the task already maps to a known origin (for example people or company research → StableEnrich, image generation → StableStudio). In those cases go straight to **discover_api_endpoints**.

CLI equivalent: `npx agentcash@latest search "<query>"`.

### 2. Discover endpoints

```mcp
agentcash.discover_api_endpoints(url="https://stableenrich.dev")
```

Returns all endpoints, pricing, and usage instructions. **Read the `instructions` field** — it has critical endpoint-specific guidance.

### 3. Check schema (optional)

```mcp
agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/apollo/people-search")
```

Returns full request/response JSON schemas and pricing for a specific endpoint.

### 4. Make a paid request

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/apollo/people-search",
  method="POST",
  body={"person_titles": ["CEO"], "person_locations": ["San Francisco"]}
)
```

Payment is automatic: sends request, gets 402 challenge, signs USDC payment, retries with credential, returns result. Payments settle only on success (2xx) — failed requests cost nothing.
`agentcash.fetch` also handles SIWX-authenticated endpoints automatically when the route supports that flow.

## Available Services

| Origin | Service | What it does |
|---|---|---|
| `https://stableenrich.dev` | StableEnrich | Research APIs: Apollo (people/org), Minerva (identity/enrichment), Exa (web search), Firecrawl (scraping), Cloudflare (site crawling), Google Maps, Clado (contacts), Serper (news/shopping), WhitePages, Reddit, Hunter (email verification), Influencer enrichment |
| `https://stableupload.dev` | StableUpload | File hosting (10MB/$0.02, 100MB/$0.20, 1GB/$2.00) + static site hosting with custom domains |
| `https://stablestudio.dev` | StableStudio | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan |
| `https://stablesocial.dev` | StableSocial | Social media data: TikTok, Instagram, Facebook, Reddit. $0.06/call, async two-step |
| `https://stableemail.dev` | StableEmail | Send emails ($0.02), forwarding inboxes ($1/mo), custom subdomains ($5), programmatic mailboxes |
| `https://stablephone.dev` | StablePhone | AI phone calls ($0.54), phone numbers ($20), top-ups ($15), iMessage/FaceTime lookup ($0.05) |
| `https://stablejobs.dev` | StableJobs | Job search via Coresignal |

Run `agentcash.discover_api_endpoints(url="<origin>")` on any origin to see its full endpoint catalog.

## Quick Reference

| Task | Tool |
|------|------|
| Check balance | `agentcash.get_balance` |
| Get deposit links and wallet addresses | `agentcash.list_accounts` |
| Redeem code | `agentcash.redeem_invite(code="...")` |
| Find APIs by natural language | `agentcash.search(query="...")` |
| Discover endpoints | `agentcash.discover_api_endpoints(url="...")` |
| Check pricing/schema | `agentcash.check_endpoint_schema(url="...")` |
| Paid POST request | `agentcash.fetch(url="...", method="POST", body={...})` |
| Paid GET request | `agentcash.fetch(url="...")` |
| Authenticated GET (no payment) | `agentcash.fetch(url="...")` |

## Tips

- Use **search** when you are unsure which service to use; use **discover_api_endpoints** when you already know the origin URL.
- Always discover before calling arbitrary paths — the `instructions` field has critical endpoint-specific patterns and required parameters.
- Payments settle only on success (2xx) — failed requests cost nothing.
- Use `check_endpoint_schema` when unsure about request/response format.
- Independent `agentcash.fetch` calls can run in parallel for better throughput.
- Network: Base (eip155:8453) or Solana (solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp), Currency: USDC.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Reinstall MCP, restart IDE |
| "Insufficient balance" | Run `agentcash.get_balance()`, then `agentcash.list_accounts()` or redeem an invite code |
| "Payment failed" | Transient error — retry the request |
| "Invalid invite code" | Code already used or doesn't exist |
| Balance not updating | Wait for Base or Solana network confirmation (~2 sec) |
