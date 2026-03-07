---
name: agentcash
description: |
  Pay-per-call access to premium APIs via x402 micropayments (USDC on Base).
  Run `npx agentcash discover <origin>` to get endpoints, pricing, and usage instructions for any service below.

  AVAILABLE SERVICES:
  - stableenrich.dev — people/company search, LinkedIn scraping, Google Maps, Exa web search, Firecrawl web scraping, Twitter/X search, GTM & sales prospecting (name → contact info)
  - stablesocial.dev — social media data (Twitter, Instagram, TikTok, YouTube, Facebook, Reddit)
  - stablestudio.dev — AI image & video generation
  - stableupload.dev — file hosting & sharing
  - stableemail.dev — send emails
  - stabletravel.dev — travel search

  TRIGGERS: research, enrich, scrape, generate image, generate video, social data, send email, travel, look up, prospect, "find info about", "who is", "find contact"
---

# agentcash — Paid API Access

You have a USDC wallet (Base) that lets you call premium APIs via x402 micropayments.
No API keys, no subscriptions — just a funded wallet.

## Wallet

| Task | Command |
|------|---------|
| Check balance | `npx agentcash wallet info` |
| Redeem invite code | `npx agentcash wallet redeem <code>` |
| Deposit | Send USDC (Base) to your wallet address |

If your balance is 0, tell the user they need to deposit USDC (Base) to their wallet address or redeem an invite code.

## Using Services

### 1. Discover endpoints on a service

```
npx agentcash discover <origin>
```

Example: `npx agentcash discover https://stableenrich.dev`

Read the output carefully — it includes endpoint paths, pricing, required parameters, and usage instructions.

### 2. Check a specific endpoint

```
npx agentcash check <endpoint-url>
```

Returns schema, pricing, and example payloads.

### 3. Make a paid request

```
# POST
npx agentcash fetch <url> -m POST -b '{"key": "value"}'

# GET
npx agentcash fetch '<url>?param=value'
```

## Important Rules

- **Always discover before guessing.** Endpoint paths include provider prefixes (e.g. `/api/apollo/people-search`, not `/people-search`). Run discover first.
- **Read the instructions field.** Discovery output includes endpoint-specific guidance — required field ordering, multi-step workflows, polling patterns, etc.
- **Payments settle on success only.** Failed requests (non-2xx) don't cost anything.
- **Check balance before expensive operations.** Video generation can cost $1-3 per call.
