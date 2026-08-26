---
name: pulse-network
description: |
  Vertical intelligence APIs across 67 x402 origins (827 paid endpoints): compliance screening, crypto/token safety, SEC filings, tax, legal, climate, health, sports, and more. Deterministic primitives from $0.01.

  USE FOR:
  - Sanctions/OFAC screening, EU VAT validation, GLEIF LEI lookups
  - Pre-trade token safety scans (Solana memecoins, EVM tokens, multi-chain)
  - SEC/EDGAR fundamentals, filing search, 13F due diligence
  - Perp funding rates, arbitrage math, FX-premium signals (Hyperliquid, dYdX)
  - Tax rates and VAT/GST for 195 countries; global visa/immigration checks
  - FDIC bank-health checks, credit-card benefit coverage
  - Real-time weather/air quality, earthquake checks, country risk
  - Deep verticals: healthcare prices, clinical trials, grants, patents, real estate, transit, racing, esports — see rules/origins.md
  - Send real physical mail (USPS letters, certified mail with tracking) — an ACTION endpoint, not a lookup

  TRIGGERS:
  - "sanctions check", "OFAC", "screen this entity", "validate VAT", "LEI"
  - "is this token safe", "memecoin check", "rug check", "scan token"
  - "SEC filing", "10-K", "13F", "company fundamentals"
  - "funding rate", "arbitrage", "stablecoin premium"
  - "tax rate in", "VAT in", "visa requirements"
  - "is my bank safe", "FDIC", "card benefit"
  - "country risk", "travel safety", "air quality"
  - "send a letter", "mail this", "certified mail", "USPS tracking"

  All origins share one pattern: `https://<vertical>.theaslangroupllc.com`. Use agentcash.discover_api_endpoints to enumerate any origin, and agentcash.fetch to call endpoints.
metadata:
  version: 1
---

# PulseNetwork — Vertical Intelligence APIs

67 single-purpose x402 API origins (827 paid endpoints) operated by The Aslan Group LLC, covering finance, compliance, legal, tax, health, climate, energy, sports, and consumer verticals. USDC on Base mainnet (eip155:8453), Coinbase Developer Platform facilitator. No accounts, no API keys.

Catalog: https://pulsenetwork.theaslangroupllc.com · Every origin serves `/openapi.json`, `/.well-known/agent.json`, and `/llms.txt`.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference — flagship endpoints

| Task | Endpoint | Price |
|------|----------|-------|
| OFAC sanctions screen (alias-aware, deterministic) | `https://compliancepulse.theaslangroupllc.com/api/screen/sanctions` | $0.02 |
| EU VAT number validation (VIES, deterministic) | `https://compliancepulse.theaslangroupllc.com/api/validate/vat` | $0.01 |
| Solana memecoin pre-trade scan (deterministic verdict) | `https://onchainpulse.theaslangroupllc.com/api/memecoin` | $0.015 |
| EVM token pre-trade scan (multi-chain) | `https://onchainpulse.theaslangroupllc.com/api/evmtoken` | $0.015 |
| SEC XBRL fundamentals — 8 quarters, deterministic | `https://filingspulse.theaslangroupllc.com/api/filings/quarterly` | $0.02 |
| Perp funding-rate check (cross-venue normalized) | `https://cryptopulse.theaslangroupllc.com/api/funding-check` | $0.02 |
| Stablecoin parallel-rate FX premium signal | `https://arbipulse.theaslangroupllc.com/api/fx-premium` | $0.02 |
| FDIC bank-health check (Call Report primitive) | `https://wealthpulse.theaslangroupllc.com/api/wealth/bank-check` | $0.02 |
| Country tax system overview (195 countries) | `https://taxpulse.theaslangroupllc.com/api/tax/country` | $0.10 |
| Earthquake activity check (USGS, deterministic) | `https://riskpulse.theaslangroupllc.com/api/risk/quake-check` | $0.01 |
| Real-time air quality + health risk | `https://climatepulse.theaslangroupllc.com/api/climate/air` | $0.05 |
| Country risk assessment | `https://geopoliticalpulse.theaslangroupllc.com/api/geopolitical/country-risk` | $0.15 |
| AI-visibility check of any website (verbatim evidence) | `https://marketpulse.theaslangroupllc.com/api/market/ai-visibility-check` | $0.50 |
| Send a real physical letter — USPS, first-class/certified/international (ACTION: prints & mails on payment) | `https://mailpulse.theaslangroupllc.com/api/letters` | $3–$14 |
| Verify a mailing address before sending physical mail (USPS-backed) | `https://mailpulse.theaslangroupllc.com/api/verify-address` | $0.01 |

Full directory of all 67 origins grouped by category: [rules/origins.md](rules/origins.md).

## Workflow

1. **Pick an origin** from the task → category mapping in [rules/origins.md](rules/origins.md) (or the Quick Reference above).
2. **Discover** its endpoints and prices:
   ```mcp
   agentcash.discover_api_endpoints("https://compliancepulse.theaslangroupllc.com")
   ```
3. **Check** a specific endpoint's schema and price before first use:
   ```mcp
   agentcash.check_endpoint_schema("https://compliancepulse.theaslangroupllc.com/api/screen/sanctions")
   ```
4. **Fetch** (most endpoints are GET with query params):
   ```mcp
   agentcash.fetch(url="https://compliancepulse.theaslangroupllc.com/api/screen/sanctions?name=ACME%20Trading%20Ltd")
   ```

## Examples

Sanctions screen before a payment:

```mcp
agentcash.fetch(url="https://compliancepulse.theaslangroupllc.com/api/screen/sanctions?name=Example%20Corp")
```

Token safety scan before a trade:

```mcp
agentcash.fetch(url="https://onchainpulse.theaslangroupllc.com/api/memecoin?mint=<SOLANA_MINT_ADDRESS>")
```

Eight quarters of reported fundamentals for a ticker:

```mcp
agentcash.fetch(url="https://filingspulse.theaslangroupllc.com/api/filings/quarterly?company=NVDA")
```

Country tax overview:

```mcp
agentcash.fetch(url="https://taxpulse.theaslangroupllc.com/api/tax/country?country=Portugal")
```

## Notes

- Unpaid requests return a standard x402 402 challenge (before body validation) with exact price and pay-to details.
- Endpoints marked "deterministic" return primary-source data with no LLM synthesis — stable output for a given input, suitable for agent pipelines.
- Upstream failures return 4xx/5xx without settling payment.
- Questions: info@theaslangroupllc.com
