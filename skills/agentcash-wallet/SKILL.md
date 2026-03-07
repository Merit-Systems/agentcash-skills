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
  - stablephone.dev — AI phone calls
  - stablejobs.dev — job search
  - stabletravel.dev — travel search
  - twit.sh — fast X/Twitter search & scraping

  TRIGGERS: research, enrich, scrape, generate image, generate video, social data, send email, travel, look up, prospect, "find info about", "who is", "find contact"
---

# agentcash — Paid API Access

Call any x402-protected API with automatic payment. No API keys, no subscriptions — just a funded wallet (USDC on Base).

## Wallet

| Task | Command |
|------|---------|
| Check balance | `npx agentcash wallet info` |
| Redeem invite code | `npx agentcash wallet redeem <code>` |
| Deposit | Send USDC (Base) to your wallet address |

If your balance is 0, tell the user they need to deposit USDC (Base) to their wallet address or redeem an invite code.

## Using Services

### 1. Discover endpoints on a service

```bash
npx agentcash discover <origin>
```

Example: `npx agentcash discover https://stableenrich.dev`

Read the output carefully — it includes endpoint paths, pricing, required parameters, and usage instructions. **Read the `instructions` field** — it has critical endpoint-specific guidance.

### 2. Check a specific endpoint (optional)

```bash
npx agentcash check <endpoint-url>
```

Returns full request/response JSON schemas and pricing.

### 3. Make a paid request

```bash
# POST
npx agentcash fetch <url> -m POST -b '{"key": "value"}'

# GET
npx agentcash fetch '<url>?param=value'
```

Payment is automatic: sends request, gets 402 challenge, signs USDC payment, retries with credential, returns result.

### 4. Fetch with wallet authentication (for async jobs)

```bash
npx agentcash fetch-auth <url>
```

Some endpoints use SIWX (Sign-In With X) wallet authentication instead of x402 payment. Use `fetch-auth` for these — it signs a wallet proof automatically. Common pattern for async services (stablestudio.dev, stablesocial.dev): `fetch` to submit a job (paid), then `fetch-auth` to poll for results (authenticated, not paid).

## Available Services

| Origin | What it does |
|--------|-------------|
| `stableenrich.dev` | Apollo (people/org search), Exa (web search), Firecrawl (scraping), Google Maps, Clado (LinkedIn), Serper (news/shopping), WhitePages, Hunter (email verification), Grok (X/Twitter) |
| `stablesocial.dev` | Social media data: TikTok, Instagram, Facebook, Reddit, LinkedIn ($0.06/call, async two-step) |
| `stablestudio.dev` | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan |
| `stableupload.dev` | Pay-per-upload file hosting (10MB/$0.02, 100MB/$0.20, 1GB/$2.00, 6-month TTL) |
| `stableemail.dev` | Send emails ($0.02), forwarding inboxes ($1/mo), custom subdomains ($5) |
| `stablephone.dev` | AI phone calls ($0.54), phone numbers ($20), top-ups ($15) |
| `stablejobs.dev` | Job search via Coresignal |
| `stabletravel.dev` | Travel search |
| `twit.sh` | Fast X/Twitter search and scraping |

Run `npx agentcash discover <origin>` on any origin to see its full endpoint catalog.

## Important Rules

- **Always discover before guessing.** Endpoint paths include provider prefixes (e.g. `/api/apollo/people-search`, not `/people-search`). Run discover first.
- **Read the instructions field.** Discovery output includes endpoint-specific guidance — required field ordering, multi-step workflows, polling patterns, etc.
- **Payments settle on success only.** Failed requests (non-2xx) don't cost anything.
- **Check balance before expensive operations.** Video generation can cost $1-3 per call.

## Tips

- Use `npx agentcash check <url>` when unsure about request/response format.
- Add `--format json` for machine-readable output, `--format pretty` for human-readable.
- Network: Base (eip155:8453), Currency: USDC.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Insufficient balance" | Check balance, deposit USDC or redeem invite code |
| "Payment failed" | Transient error — retry the request |
| "Invalid invite code" | Code already used or doesn't exist |
| Balance not updating | Wait for Base network confirmation (~2 sec) |
