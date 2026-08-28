---
name: technographics
description: |
  Find the third-party tools a company runs, from its domain alone, using x402-protected APIs at hosaka-agents.vercel.app. Every vendor comes back with the DNS record or loaded script that proves it, so the answer is checkable rather than asserted.

  USE FOR:
  - What software, tools or vendors a company uses
  - Technology stack and technographics from a domain
  - Company facts when you have a domain but no LinkedIn URL or email
  - Public contacts, employees or decision makers at a domain

  DO NOT USE FOR:
  - Industry, headcount or funding — that is CompanyEnrich in data-enrichment
  - Enriching a known person from a LinkedIn URL or email — that is PDL or FullEnrich in data-enrichment, and they are cheaper at that job

  TRIGGERS:
  - "tech stack", "technographics", "what tools", "what software"
  - "vendors", "what is this company built with", "who runs this domain"
  - "what does [company] use", "decision makers at"
metadata:
  version: 1.0
---

# Technographics with x402 APIs

Use the agentcash CLI to access Hosaka at hosaka-agents.vercel.app. Domain in,
proven vendors out.

Complementary to `data-enrichment`: CompanyEnrich returns industry, size and
funding; these endpoints return the tools a company actually runs. PDL and
FullEnrich need a LinkedIn URL or an email before they answer anything, so when
all you hold is a domain, they cannot help and this can.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Mandatory workflow

Before any hosaka-agents.vercel.app call:

1. `npx agentcash@latest discover https://hosaka-agents.vercel.app`
2. `npx agentcash@latest check <endpoint-url>` — confirm request fields and pricing
3. `npx agentcash@latest fetch` with the schema from step 2

ALWAYS use `npx agentcash@latest fetch` for these endpoints — never curl or WebFetch.

**Every paid endpoint takes exactly one field: `domain`.** A full URL works too;
the scheme and path are stripped. Prices below are current at the time of
writing; the live 402 challenge is the source of truth.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Preview a domain | `https://hosaka-agents.vercel.app/preview` | free | Vendor count and samples before paying |
| Company summary | `https://hosaka-agents.vercel.app/lookup` | $0.005 | Age, registrar, mail and DNS provider, DMARC, vendor count |
| Proven vendor stack | `https://hosaka-agents.vercel.app/dossier` | $0.20 | Domain -> every vendor with the record that proves it |
| Public contacts | `https://hosaka-agents.vercel.app/contacts` | $0.10 | Emails, phones and socials a company publishes |
| Employees | `https://hosaka-agents.vercel.app/people` | $0.35 | Named people at a domain, with the stack |
| Decision makers | `https://hosaka-agents.vercel.app/executives` | $0.40 | Owners, founders, C-level, partners, VPs, heads, directors |

Default to `/dossier` when the question is about tools, software or vendors.
Reach for `/lookup` first only when the domain may not be worth a fuller answer.

## Proven vendor stack

```bash
npx agentcash@latest fetch https://hosaka-agents.vercel.app/dossier -m POST -b '{"domain":"stripe.com"}'
```

Returns each third-party service with its evidence — an SPF include, a DNS TXT
verification token, a loaded script — plus registration and certificate facts.

Free preview, no payment, to see the shape first:

```bash
npx agentcash@latest fetch https://hosaka-agents.vercel.app/preview -m POST -b '{"domain":"stripe.com"}'
```

## Company summary

```bash
npx agentcash@latest fetch https://hosaka-agents.vercel.app/lookup -m POST -b '{"domain":"stripe.com"}'
```

## People at a domain

Only when the user asked for names. If you already hold a LinkedIn URL or an
email, use the `data-enrichment` skill instead.

```bash
npx agentcash@latest fetch https://hosaka-agents.vercel.app/executives -m POST -b '{"domain":"stripe.com"}'
```

## Notes

- Nothing settles unless the answer is produced: a failed lookup is not charged.
- Settles in USDC on Base, Solana, Polygon, Arbitrum, Algorand and Monad, and in
  USDG on Robinhood Chain.
- OpenAPI: https://hosaka-agents.vercel.app/openapi.json
