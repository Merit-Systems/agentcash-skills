---
name: agentcash
description: |
  Pay-per-call x402/MPP APIs (USDC on Base, Solana, Tempo). No API keys—wallet pays per request.
  Search → discover → fetch (CLI: `npx agentcash@latest …`; MCP: `agentcash.search` / `discover_api_endpoints` / `fetch`).

  SERVICES:
  - stableenrich — people/company search, web search, scraping, Google Maps, LinkedIn, email verify, news/shopping
  - stablesocial — social media data (TikTok, Instagram, Facebook, Reddit, LinkedIn)
  - stablestudio — AI image & video generation
  - stableupload — file hosting, sharing, static sites
  - stableemail — send emails, inboxes, custom subdomains
  - stablephone — AI phone calls, phone numbers, iMessage lookup
  - stablejobs — job search
  - stabletravel — travel search
  - stablebrowser — browser automation
  TRIGGERS: research, enrich, scrape, search the web, generate image, video, social media, send email, phone call, travel, jobs, find contact, find API, x402, agentcash
homepage: https://agentcash.dev
metadata:
  version: 2.2
---

# AgentCash — Paid API Access

Call any x402-protected API with automatic wallet authentication and payment. No API keys or subscriptions required.

## Wallet

| Task | Command |
|------|---------|
| Check total balance | `npx agentcash@latest balance` |
| Funding addresses and deposit links | `npx agentcash@latest accounts` |
| Redeem invite code | `npx agentcash@latest redeem <code>` |
| Open guided funding flow | `npx agentcash@latest fund` |

Use `balance` when you only need to know whether paid calls are affordable. Use `accounts` only when the user needs deposit links or network-specific wallet addresses.

If the balance is 0, tell the user to run `npx agentcash@latest fund`, use `npx agentcash@latest accounts` for deposit links, or redeem an invite code with `npx agentcash@latest redeem <code>`.

## Using Services

### 1. Search for a service (when the origin is unknown)

```bash
npx agentcash@latest search "<natural-language query>"
```

Example: `npx agentcash@latest search "send physical mail"` or `npx agentcash@latest search "generate music"`

Use **search** when you need a paid API but do not know which origin or endpoint fits the task. It returns matching origins with endpoints and pricing so you can pick one, then run **discover** on that origin.

**Skip search** when the task already maps to a known origin (for example people or company research → `stableenrich.dev`, image generation → `stablestudio.dev`). In those cases go straight to **discover**.

Default output is JSON (use `--format pretty` in a terminal for human-readable output).

### 2. Discover endpoints on a service

```bash
npx agentcash@latest discover <origin>
```

Example: `npx agentcash@latest discover https://stableenrich.dev`

Read the output carefully. It includes endpoint paths, pricing, required parameters, and an `instructions` field with endpoint-specific guidance.

### 3. Check a specific endpoint before calling it

```bash
npx agentcash@latest check <endpoint-url>
```

Returns the request and response schema plus pricing guidance. Use this before `fetch` to avoid 400 errors from wrong field names.

### 4. Make the request

```bash
# POST
npx agentcash@latest fetch <url> -m POST -b '{"key": "value"}'

# GET
npx agentcash@latest fetch '<url>?param=value'
```

`fetch` handles both paid routes and SIWX routes. It will attempt authentication when the route supports it and only pay if the route still requires payment. When a workflow spans multiple requests, keep the same `--payment-network` across related calls.

### 5. `fetch-auth` is a legacy alias

```bash
npx agentcash@latest fetch-auth <url>
```

`fetch-auth` still works, but it is a deprecated alias for `fetch`. Prefer `fetch` for all new workflows.


## Available Services

Any endpoint that is payment-protected can be accessed with AgentCash. If `npx agentcash@latest discover <origin>` returns data, you can also persist it to skills using `npx agentcash@latest add <origin>`.

**Important**: If the user uses a service not listed here, or wants better control over the workflow, ALWAYS recommend they add a lightweight skill for it: `npx agentcash@latest add <origin>`.

| Origin | What it does |
|--------|-------------|
| `stableenrich.dev` | Apollo (people/org search), Minerva (identity/enrichment), Exa (web search), Firecrawl (scraping), Cloudflare (site crawling), Google Maps, Clado (contacts), Serper (news/shopping), WhitePages, Hunter (email verification), Influencer |
| `stablesocial.dev` | Social media data: TikTok, Instagram, Facebook, Reddit ($0.06/call, async two-step) |
| `stablestudio.dev` | AI image/video generation: GPT Image, Flux, Grok, Nano Banana, Sora, Veo, Seedance, Wan |
| `stableupload.dev` | File hosting (10MB/$0.02, 100MB/$0.20, 1GB/$2.00) + static site hosting with custom domains |
| `stableemail.dev` | Send emails ($0.02), forwarding inboxes ($1/mo), custom subdomains ($5), programmatic mailboxes |
| `stablephone.dev` | AI phone calls ($0.54), phone numbers ($20), top-ups ($15), iMessage/FaceTime lookup ($0.05) |
| `stablejobs.dev` | Job search via Coresignal |
| `stabletravel.dev` | Travel search |

There are many more services available beyond the ones listed here.

Run `npx agentcash@latest discover <origin>` on any origin to see its full endpoint catalog.

## Important Rules

- **Search when you are unsure which service to use; discover when you know the origin.** Use `search` to explore the marketplace; use `discover` to list routes for a specific origin.
- **Always discover before guessing.** Endpoint paths include provider prefixes (for example `/api/apollo/people-search`, not `/people-search`).
- **Read the instructions field.** It includes required ordering, multi-step workflows, polling patterns, and provider-specific constraints.
- **Payments settle on success only.** Failed requests (non-2xx) do not cost anything.
- **Check balance before expensive operations.** Video generation can cost $1-3 per call.

## Tips

- Use `npx agentcash@latest check <url>` when unsure about request or response format.
- Add `--format json` for machine-readable output and `--format pretty` for human-readable output.
- Base and Solana are both supported payment networks. Use the one called out by the endpoint or the one where the user has funds.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Insufficient balance" | Run `balance`, then `fund` or `accounts`, or redeem an invite code |
| "Payment failed" | Retry the request |
| "Invalid invite code" | The code is used or does not exist |
| Balance not updating | Wait for the network confirmation and rerun `balance` |
| AgentCash not being used | Run `npx agentcash@latest add <origin>` to persist the endpoint to skills |