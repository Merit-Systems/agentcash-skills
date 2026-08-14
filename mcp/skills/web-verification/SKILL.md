---
name: web-verification
description: |
  Verify a web value you already hold against its live source page using
  ScrapeCheck's x402-protected API. Not retrieval: it checks a value, it
  does not find data. Returns a signed pass/fail/unverifiable verdict
  anyone can verify offline.

  USE FOR:
  - Checking a scraped or purchased value is on its source page right now
  - Validating another tool's output (search result, scraper, upstream API)
  - Confirming a price, title, or availability before acting on it
  - Producing signed, offline-verifiable evidence of what a page said
  - Cheap presence screening before a full check

  TRIGGERS:
  - "verify", "double-check", "confirm this is on the page"
  - "is this price right", "is this still listed", "still in stock"
  - "check the scraper's output", "validate this data"
  - "signed verdict", "proof", "evidence it was on the page"

  Use the agentcash MCP fetch tool against scrapecheck.fly.dev. Full
  verification answers "is this the right value"; presence only answers
  "does it appear at all" and never returns pass.
metadata:
  version: 1
---

# Web Verification with ScrapeCheck

Check whether a value you did not fetch yourself is actually on the page
it came from. ScrapeCheck independently re-fetches the source and returns
an ed25519-signed pass/fail/unverifiable verdict. A claim is never
certified unless the re-fetched page contains it, and anything
unconfirmed is unverifiable — never converted to a pass.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation
and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Full verification | `https://scrapecheck.fly.dev/verify` | $0.01 | Is this value on the page AND the answer to what was asked |
| Presence screen | `https://scrapecheck.fly.dev/verify-presence` | $0.002 | Does the value appear at all (never returns pass) |
| Public key | `GET https://scrapecheck.fly.dev/pubkey` | Free | Verify signatures offline |
| Issuance check | `GET https://scrapecheck.fly.dev/verdicts/{verdict_id}` | Free | Confirm a verdict_id was really issued |

First 100 checks per client are free: send header
`X-Use-Free-Allowance: yes` with no payment. Past the allowance, the
x402 payment is the credential — no account, no API key.

## When to Use What

| Scenario | Tool |
|----------|------|
| You need to FIND data | A search/scraping skill, then verify the result here |
| You hold a value and are about to act on it | `/verify` |
| Cheap screen across many rows first | `/verify-presence`, then `/verify` the survivors |
| The page is JS-only/client-rendered | Verdict will be unverifiable by design — ScrapeCheck never guesses |

## Full Verification

```mcp
agentcash.fetch(
  url="https://scrapecheck.fly.dev/verify",
  method="POST",
  body={
    "url": "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html",
    "claim": { "price": "£51.77" },
    "asked": "get the current price of A Light in the Attic"
  }
)
```

Returns a signed verdict: `verdict` (pass | fail | unverifiable),
`confidence`, `reasons`, `evidence` (re-fetch excerpt + timestamp),
`verdict_id`, `key_id`, `source_hash`, `signature`.

## Presence Screen

```mcp
agentcash.fetch(
  url="https://scrapecheck.fly.dev/verify-presence",
  method="POST",
  body={
    "url": "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html",
    "claim": { "price": "£51.77" },
    "asked": "context only"
  }
)
```

Presence confirms the value APPEARS on the page, not that it answers the
question — a was-price, another variant's price, or a shipping cost can
all satisfy presence. Positive verdict is `present`, never `pass`.

## Verify a Verdict Offline

Every verdict is ed25519-signed over canonical sorted-key JSON of every
field except `signature`. Open verifier (MIT, zero dependencies) with a
real example:
https://github.com/FieldmodeLLC/scrapecheck-mcp#verify-a-verdict-yourself-offline

You do not have to trust ScrapeCheck at runtime to rely on its output.

## Scope

Server-rendered public pages only. JS-only content returns unverifiable
rather than a guess. Live verdict mix including fails and unverifiables:
https://scrapecheck.fly.dev/stats
