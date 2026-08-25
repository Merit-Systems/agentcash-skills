---
name: bavlio
description: |
  Verify email deliverability, find working emails, and discover LinkedIn profile URLs via x402 payments. B2B data endpoints for agents.

  USE FOR:
  - SMTP-verifying an email address before outreach (catch-all + MX + RCPT probe)
  - DNS-only deliverability check (syntax + MX + disposable + role) without an SMTP probe
  - Finding a working email address from first_name + last_name + domain (sequential SMTP pattern probing)
  - Finding a LinkedIn profile URL from name + company via public search engines
  - Searching a shared prospect pool by tag overlap (read-only B2B lookups)

  TRIGGERS:
  - "verify email", "is this email real", "check deliverability"
  - "validate email", "MX lookup", "is this disposable"
  - "find email", "guess email", "what's their email", "pattern probe"
  - "find linkedin", "linkedin profile URL", "who is this person on linkedin"
  - "search prospects", "find people by tag", "b2b lead search"

  ALWAYS use `npx agentcash@latest fetch` for api.bavlio.com endpoints.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below. All five endpoints are under `/api/x402/v1/` on host `https://api.bavlio.com`.
metadata:
  version: 1
---

# Bavlio — B2B Data API

Verify email deliverability, find emails by pattern, discover LinkedIn URLs, and search a shared prospect pool via x402 payments at `https://api.bavlio.com`. USDC on Base mainnet. No account, no API key.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| SMTP-verify one email | `https://api.bavlio.com/api/x402/v1/email/verify` | $0.005 |
| DNS-only validate one email | `https://api.bavlio.com/api/x402/v1/email/validate` | $0.003 |
| Find working email (pattern probe) | `https://api.bavlio.com/api/x402/v1/email/find` | $0.010 |
| Find LinkedIn URL for a person | `https://api.bavlio.com/api/x402/v1/linkedin/find-url` | $0.008 |
| Search prospect pool by tag overlap | `https://api.bavlio.com/api/x402/v1/prospects/search` | $0.012 |

Network: Base mainnet. Merchant: `0xCca4725e0e931982830bff523b141E1A7a58d3E7`. Facilitator: Coinbase Developer Platform (KYT + OFAC screening handled automatically).

All endpoints return structured JSON. Adapter-timeout / upstream-failure responses return 4xx or 5xx without settling payment (x402 protocol guarantee).

## Verify deliverability (SMTP RCPT probe)

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/email/verify -m POST -b '{
  "email": "prospect@acme.com"
}'
```

Response shape:

```json
{
  "email": "prospect@acme.com",
  "result": "valid",
  "is_catch_all": false,
  "backend": "reacher"
}
```

`result` is one of `"valid" | "invalid" | "risky" | "unknown"`. Use this before committing to a paid outreach wave to cut bounce rate.

## DNS-only validate (cheaper preflight)

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/email/validate -m POST -b '{
  "email": "maybe@example.com"
}'
```

Response includes `valid_format`, `has_mx`, `is_disposable`, `is_role`, `risk_level` (`"safe"|"risky"|"invalid"`). ~500ms typical. No SMTP probe — useful as a cheap preflight before the paid RCPT verify above.

## Find working email (pattern probe)

Either pass `candidates` or `first_name + last_name + domain` and the service generates candidate patterns.

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/email/find -m POST -b '{
  "first_name": "Dan",
  "last_name": "Roberts",
  "domain": "acme.com"
}'
```

With pre-computed candidates (e.g. from a LinkedIn lookup):

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/email/find -m POST -b '{
  "candidates": [
    {"email": "d.roberts@acme.com", "pattern": "f.last", "confidence": 0.6},
    {"email": "dan@acme.com", "pattern": "first", "confidence": 0.4}
  ],
  "domain": "acme.com"
}'
```

Response: `{ "found": true, "email": "...", "pattern": "first.last", "result": "valid", "backend": "reacher" }`. On exhaustion without a match: `{ "found": false, "backend": "reacher" }` — this is still a paid answer (the question was answered). Circuit-breaker trips return 503 without settlement.

## Find LinkedIn URL

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/linkedin/find-url -m POST -b '{
  "name": "Jane Doe",
  "company": "Tesla",
  "location": "us"
}'
```

`name` is required (1-120 chars, whitespace-stripped). `company` is optional (improves match confidence). `location` is a 2-letter ASCII country code biasing the search region (default `us`). No LinkedIn-proprietary data is scraped or returned — discovery is via public search engines with a residential proxy.

Response: `{ "status": "found", "query": "...", "linkedin_url": "https://linkedin.com/in/...", "profiles": [{ "match_confidence": 0.82, "match_reason": "name + company token match", ... }] }`. If all candidates score below the 0.3 confidence threshold, `status` is `"not_found"` with empty `linkedin_url`.

## Search prospect pool

```bash
npx agentcash@latest fetch https://api.bavlio.com/api/x402/v1/prospects/search -m POST -b '{
  "tags": ["founder", "ai"],
  "limit": 50
}'
```

Read-only tag-overlap query against a shared prospect pool. `tags` is 1-25 entries (alphanumeric + space + hyphen + underscore, 1-64 chars each). `limit` default 20, max 100.

Response: `{ "count": N, "results": [{ "id", "linkedin_url", "full_name", "headline", "title", "company", "location", "tags": [...] }, ...] }`. A `count: 0` with empty results is still a paid answer.

## Failure modes

- `504` with `ERR_*_TIMEOUT` — upstream timed out. No settlement. Safe to retry.
- `503` with `ERR_*_UNAVAILABLE` / `reason` — circuit breaker open (e.g., Reacher DNS down, DDGS 429, prospect-pool unreachable). No settlement. Back off and retry.
- `502` with `ERR_*_UPSTREAM` — unhandled upstream error. No settlement. Log + investigate.
- `402` with `invalidReason` — x402 signature rejected by facilitator (expired `validBefore`, replayed nonce, OFAC decline). Agent must resign and retry.
- `400` — schema error on request body. Fix payload.

All other 2xx responses are paid.

## Learn more

- Full OpenAPI spec: `https://api.bavlio.com/openapi.json` (includes `x-agent-examples` per endpoint + per-route `x-payment-info.pay_to`).
- Swagger UI: `https://api.bavlio.com/docs`.
- ReDoc: `https://api.bavlio.com/redoc`.
- Marketing page: `https://bavlio.com/developers/x402`.
