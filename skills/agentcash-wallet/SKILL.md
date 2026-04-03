---
name: agentcash
description: |
  Pay-per-call access to premium APIs via x402 micropayments (USDC on Base or Solana).
  Run `npx agentcash@latest discover <origin>` to get endpoints, pricing, and usage instructions for any service below.

  AVAILABLE SERVICES:
  - stableenrich.dev — people/company search, Minerva identity resolution, Google Maps, Exa web search, Firecrawl web scraping, Cloudflare site crawling, GTM & sales prospecting (name → contact info)
  - stablesocial.dev — social media data (TikTok, Instagram, Facebook, Reddit)
  - stablestudio.dev — AI image & video generation
  - stableupload.dev — file hosting, sharing & static site hosting
  - stableemail.dev — send emails, inboxes, custom subdomains
  - stablephone.dev — AI phone calls, iMessage/FaceTime lookup
  - stablejobs.dev — job search
  - stabletravel.dev — travel search
  TRIGGERS: research, enrich, scrape, generate image, generate video, social data, send email, travel, look up, prospect, "find info about", "who is", "find contact"
homepage: https://agentcash.dev
metadata:
  version: 2
---

# AgentCash — Wallet and Paid API Access

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

## Service Workflow

1. Run `npx agentcash@latest discover <origin>` to inspect the origin.
2. Run `npx agentcash@latest check <endpoint-url>` before calling a new endpoint.
3. Run `npx agentcash@latest fetch <url>` for both paid and SIWX routes.
4. Treat `npx agentcash@latest fetch-auth <url>` as a deprecated alias kept only for compatibility.

## Important Rules

- Discover before guessing endpoint paths.
- Read the `instructions` field from discovery output.
- Check `balance` before expensive operations.
- Use the same `--payment-network` across related requests when a workflow spans multiple calls.
