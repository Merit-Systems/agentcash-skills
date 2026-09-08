---
name: ghost-web-search
description: |
  Verified Web Search for AI agents via x402: the actual web results in ONE payment ($0.01 USDC on Base), with automatic failover between two Google SERP providers, provenance naming the provider that answered, and a DSSE-signed receipt over the query and results. A search that fails after payment is never kept — the fee returns as credit.

  USE FOR:
  - Web search and research when you need results, not a referral
  - Current information: news, releases, status of a project or protocol
  - Source-backed factual answers (returns titles, links, snippets, answer box, related questions)
  - Work that must be auditable later (every answer carries a signed receipt naming the provider)

  TRIGGERS:
  - "search the web", "look up", "find recent", "latest", "what's new"
  - "research", "find sources", "current status", "recent developments"
  - "is this still true", "what happened with", "news about"

  Use `npx agentcash@latest fetch` for ghost-identity.ghost-agent-os.workers.dev endpoints. One paid call returns the results; there is no second payment.
metadata:
  version: 1
---

# Ghost — Verified Web Search

Origin: `https://ghost-identity.ghost-agent-os.workers.dev`
Payment: x402 v2, `exact` / `eip155:8453` (Base), USDC. $0.01 per search, any result count 1–100.

## Workflow

1. **Discover** (once per session):
   ```bash
   npx agentcash@latest discover https://ghost-identity.ghost-agent-os.workers.dev
   ```
2. **Search**:
   ```bash
   npx agentcash@latest fetch https://ghost-identity.ghost-agent-os.workers.dev/v1/search \
     -m POST -b '{"query": "latest x402 developments", "results": 10}'
   ```
   Optional body fields: `country` (e.g. `"us"`), `language` (e.g. `"en"`).

The first response is a 402 with x402 terms; `agentcash fetch` signs and retries automatically. The 200 contains the results.

## What comes back

| Field | Meaning |
|---|---|
| `results[]` | `title`, `url`, `snippet`, `position`, `published` |
| `answer` | Featured answer text and its source URL, when present |
| `knowledge` | Knowledge-panel title, type, description, when present |
| `related_questions`, `related_searches` | Follow-up queries |
| `provider`, `provenance` | The upstream that actually served this answer |
| `attempts[]` | Which providers were tried, which failed, which delivered |
| `receipt` | DSSE-signed statement over the query, the returned URLs and the provider |
| `billing` | `payments_required: 1` — one payment to Ghost, no provider key or second charge |

## Guarantees, stated plainly

- **One payment.** Ghost calls the search provider on its own commercial account. You hold no provider account and no API key.
- **Failover.** Serping is primary, Serper is secondary. A dead upstream costs a retry, not an answer.
- **Failed delivery protection.** If no provider delivers after settlement, you get a 502 and the fee is returned as Ghost credit (`GET /v1/gate/credit`; spend it with `X-Ghost-Credit-Token`).
- **Limits.** Results are Google SERP data — titles, links, snippets — not full page content.

## Other endpoints on the same origin ($0.01 each)

- `POST /v1/gate/decide` — pre-spend ALLOW / FALLBACK / DECLINE for an x402 provider
- `POST /v1/vet` — one seller's settlement history, payer structure, funding links
- `POST /v1/commerce/search` — ranked x402 providers with economic evidence
- `POST /v1/check` — has a published OpenAPI/JSON Schema changed since a fingerprint

Free preflights: `POST /v1/vet/preflight`, `POST /v1/commerce/search/preflight`.
