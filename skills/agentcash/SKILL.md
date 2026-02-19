---
name: agentcash
description: Use when calling paid APIs via the agentcash CLI. Covers discovering x402-protected endpoints, checking pricing/schemas, making paid requests, and wallet management across the Stable* services. Trigger phrases include "use agentcash", "call a paid API", "fetch with payment", "discover endpoints", "check pricing", "x402", "pay for API", "USDC API". Also use when the user wants to search the web, enrich contacts, generate images/video, send email, upload files, make phone calls, or search social media — all available as pay-per-request APIs via agentcash.
---

# Paid APIs with agentcash

Call any x402-protected API with automatic payment. No API keys, no subscriptions — just a funded wallet (USDC on Base).

## Workflow

### 1. Discover endpoints

```bash
npx agentcash discover https://stableenrich.dev
```

Returns all endpoints, pricing, and usage instructions. **Read the `instructions` field** — it has endpoint-specific guidance.

### 2. Check schema (optional)

```bash
npx agentcash check https://stableenrich.dev/api/apollo/people-search
```

Returns full request/response JSON schemas and pricing for a specific endpoint.

### 3. Make a paid request

```bash
npx agentcash fetch https://stableenrich.dev/api/apollo/people-search -m POST -b '{"person_titles": ["CEO"], "person_locations": ["San Francisco"]}'
```

Payment is automatic: sends request, gets 402 challenge, signs USDC payment, retries with credential, returns result.

## Available Services

| Origin | Service | What it does |
|---|---|---|
| `https://stableenrich.dev` | StableEnrich | Research APIs: Apollo (people/org), Exa (web search), Firecrawl (scraping), Grok (X/Twitter), Google Maps, Clado (LinkedIn), Serper (news/shopping), WhitePages, Reddit, Hunter (email verification), Influencer enrichment |
| `https://stableupload.dev` | StableUpload | Pay-per-upload file hosting. 10MB/$0.02, 100MB/$0.20, 1GB/$2.00. 6-month TTL |
| `https://stablestudio.dev` | StableStudio | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan |
| `https://stablesocial.dev` | StableSocial | Social media data: TikTok, Instagram, X/Twitter, Facebook, Reddit, LinkedIn |
| `https://stableemail.dev` | StableEmail | Send emails ($0.02), buy inboxes ($1/mo), custom subdomains ($5) |
| `https://stablephone.dev` | StablePhone | AI phone calls ($0.54), buy phone numbers ($20) |
| `https://stablejobs.dev` | StableJobs | Job search via Coresignal ($0.02) |

## Wallet

```bash
npx agentcash wallet info                  # Check address and USDC balance
npx agentcash wallet redeem <invite-code>  # Redeem invite code for free USDC
```

## CLI Reference

```bash
npx agentcash discover <url>               # Discover all endpoints on an origin
npx agentcash check <url>                  # Check pricing/schema for an endpoint
npx agentcash fetch <url> -m POST -b '{}'  # Make a paid request
npx agentcash fetch <url>                  # Make a paid GET request
```

## Tips

- Always `discover` first — the instructions field has critical endpoint-specific patterns.
- Payments settle only on success (2xx) — failed requests cost nothing.
- Use `-b` for JSON request bodies, `-m` for HTTP method.
- Add `--format json` for machine-readable output, `--format pretty` for human-readable.
- Add `-v` for verbose output to see payment details.
- Network: Base (eip155:8453), Currency: USDC.
