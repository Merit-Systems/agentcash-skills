---
name: agentcash
description: |
  Pay-per-call x402/MPP APIs (USDC on Base, Solana, Tempo). No API keys—wallet pays per request.
  If the task matches a SERVICES origin below, SKIP search and go straight to discover → fetch.
  Only search when NO listed origin fits.

  SERVICES: stableenrich (people/company, web search, scraping, Maps, LinkedIn, email verify, news), stablesocial (TikTok, Instagram, YouTube, Facebook, Reddit, LinkedIn, GitHub), stablestudio (AI image/video), stableupload (file/site hosting), stableemail (email, inboxes, subdomains), stablephone (AI calls, phone numbers), stablejobs (jobs), stabletravel (travel), stablebrowser (browser automation).
  TRIGGERS: research, enrich, scrape, search the web, generate image, video, social media, send email, phone call, travel, jobs, find contact, find API, x402, mpp, agentcash
homepage: https://agentcash.dev
metadata:
  version: 3.1
---

# AgentCash — Paid API Access

Call any x402 or MPP-protected API with automatic wallet authentication and payment. No API keys or subscriptions required.

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

### 1. Pick an origin — or search

**Check the Available Services table below first.** If any origin clearly covers the task, skip search entirely and jump to step 2 (discover). Examples:

| Task | Origin (skip search) |
|------|---------------------|
| Look up a person or company | `stableenrich.dev` |
| Generate an image or video | `stablestudio.dev` |
| Get Instagram/TikTok data | `stablesocial.dev` |
| Send an email | `stableemail.dev` |
| Upload a file | `stableupload.dev` |

**Only use search when none of the listed origins fit:**

```mcp
agentcash.search(query="send physical mail")
```

Returns matching origins with endpoints and pricing (the top match often includes schema so you can call **fetch** immediately).

### 2. Discover endpoints

```mcp
agentcash.discover_api_endpoints(url="https://stableenrich.dev")
```

Returns all endpoints, pricing, and usage instructions. **Read the `instructions` field** — it has critical endpoint-specific guidance.

### 3. Check schema (optional)

```mcp
agentcash.check_endpoint_schema(url="https://stableenrich.dev/api/fullenrich/people-search")
```

Returns full request/response JSON schemas and pricing for a specific endpoint.

### 4. Make a paid request

```mcp
agentcash.fetch(
  url="https://stableenrich.dev/api/fullenrich/people-search",
  method="POST",
  body={
    "current_company_domains": [{"value": "stripe.com"}],
    "current_position_seniority_level": [{"value": "VP"}]
  }
)
```

Payment is automatic: sends request, gets 402 challenge, signs USDC payment, retries with credential, returns result. Payments settle only on success (2xx) — failed requests cost nothing.
`agentcash.fetch` also handles SIWX-authenticated endpoints automatically when the route supports that flow.

## Available Services

| Origin | Service | What it does |
|---|---|---|
| `https://stableenrich.dev` | StableEnrich | FullEnrich (people/company search), PDL & Minerva (person enrichment), CompanyEnrich (company profiles), Clado (contacts), Exa (web search), Firecrawl (scraping), Cloudflare (site crawling), Google Maps + Solar + Aerial View, Serper (news/shopping/images/lens), Whitepages, Reddit, Hunter (email verification) |
| `https://stableupload.dev` | StableUpload | File hosting ($0.005-$2.00 by size, tier-based retention) + static site hosting with custom domains |
| `https://stablestudio.dev` | StableStudio | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan, image-to-SVG |
| `https://stablesocial.dev` | StableSocial | Social media data: TikTok, Instagram, YouTube, Facebook, LinkedIn, Reddit, Rumble, GitHub, ad libraries (Scrape Creators), plus Lightreel UGC research agent. $0.06/call, async jobs |
| `https://stableemail.dev` | StableEmail | Send emails ($0.02), forwarding inboxes ($1/mo), custom subdomains ($5) |
| `https://stablephone.dev` | StablePhone | AI phone calls ($0.54), phone numbers ($20), top-ups ($15) |
| `https://stablejobs.dev` | StableJobs | Job search via Coresignal (preview $0.10/page, collect $0.20/job) |
| `https://stabletravel.dev` | StableTravel | Flight prices and booking (Google Flights), award availability (Seats.aero), live flight tracking and airport data (FlightAware) |
| `https://stablebrowser.dev` | StableBrowser | Cloud browser automation: create a session ($0.10), then AI-powered navigate/act/extract/observe/screenshot (free SIWX) |

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

- **Skip search when a listed origin fits the task.** Go straight to `discover_api_endpoints`. Only use `search` when no origin in the Available Services table matches.
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
