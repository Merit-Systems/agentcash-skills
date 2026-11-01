---
name: company-hiring-signals
description: |
  Company enrichment and hiring signals from a domain, using x402-protected APIs at gethirepulse.xyz. Pay per useful result; empty or blocked results are free.

  USE FOR:
  - Enriching a company by domain: tech stack, open jobs, hiring intent, Google Ads, published contacts
  - Filtering a list of company domains down to the ones hiring hardest
  - Bulk enrichment of up to 50 domains with one signal each
  - Tracking new and closed jobs at a list of companies
  - Turning a public web page into clean Markdown

  TRIGGERS:
  - "is [company] hiring", "open jobs at", "hiring signals", "hiring intent"
  - "tech stack of", "what does [domain] run on", "BuiltWith"
  - "company profile for", "enrich these domains", "which of these companies are hiring"
  - "Google Ads for", "is [company] advertising"
  - "web page to markdown", "read this page"
metadata:
  version: 1.0
---

# Company hiring signals with x402 APIs

Use the agentcash CLI to call the HirePulse APIs at gethirepulse.xyz. Settlement is x402 v2, USDC on Base.

## Workflow

1. `npx agentcash@latest discover https://gethirepulse.xyz`
2. `npx agentcash@latest check <endpoint-url>`, to confirm request fields and pricing
3. `npx agentcash@latest fetch`, with the schema from step 2

All single-company endpoints take a bare domain such as `ramp.com`. Never send a URL, a path or a company name.

## Quick reference

| Task | Endpoint | Price | Body |
|------|----------|-------|------|
| Company profile (tech, hiring, ads, contacts) | `https://gethirepulse.xyz/v1/company-profile` | $0.03 if 2 of 4 sections have data | `{"domain":"ramp.com"}` |
| Open jobs (Greenhouse, Lever, Ashby, Workable, Recruitee, SmartRecruiters) | `https://gethirepulse.xyz/v1/ats-jobs` | $0.01 if jobs found | `{"domain":"ramp.com"}` |
| Hiring intent score 0-100 | `https://gethirepulse.xyz/v1/company-intent` | $0.01 if a board is found | `{"domain":"ramp.com"}` |
| Website tech stack | `https://gethirepulse.xyz/v1/tech-stack` | $0.005 if detected | `{"domain":"notion.so"}` |
| Google Ads by domain | `https://gethirepulse.xyz/v1/google-ads` | $0.01 if ads found | `{"domain":"monday.com"}` |
| Published company contacts | `https://gethirepulse.xyz/v1/extract-contacts` | $0.01 if found | `{"domain":"ramp.com"}` |
| Web page to Markdown | `https://gethirepulse.xyz/v1/clean-markdown` | $0.002 | `{"url":"https://example.com"}` |

## A list of companies

- **Keep only the companies that are hiring:** `https://gethirepulse.xyz/v1/hot-buyers` with 2 to 25 domains. The first call is a free scan that returns `quote_id`. Redeeming it with `{"quote_id":"..."}` charges $0.25 per hot company, at most $1.00.
- **One signal for each of up to 50 domains:** `https://gethirepulse.xyz/v1/batch` with `{"tool":"company-intent","domains":[...]}`. This is the same free scan, then a quote for the rows with data.
- **Job changes since last time:** `https://gethirepulse.xyz/v1/watch-jobs` with `known_job_ids` per domain. $0.01 per company whose jobs changed.

## Notes

- Hiring intent is a heuristic signal, not confirmed purchase intent.
- `example.com`, `stripe.com` and `linear.app` return labeled demo data for free.
- The free advisor `POST https://gethirepulse.xyz/v1/advisor` with `{"agent_goal":"..."}` returns the right endpoint and its price.
