---
name: veradata-verified-data
description: |
  Verified Latin American data & sanctions/compliance screening for AI agents using VeraData — pay-per-call via x402 ($0.02–$0.10 USDC).
  Returns structured sanctions screening, entity enrichment, real-time central bank rates, and AI-powered LATAM market context.

  USE FOR:
  - Sanctions screening (OFAC SDN + SARLAFT Colombia + CNBV Mexico + COAF Brazil + UAF Chile, 20K+ entries)
  - Verifying a LATAM counterparty before onboarding or transacting
  - Enriching company entities (RUES/CNPJ/RFC registries)
  - Real-time central bank rates (DTF/TIIE/Selic/TRM/UF for CO/MX/BR/CL/PE)
  - EU AI Act Art.13 compliant audit hash for compliance pipelines

  TRIGGERS:
  - "sanctions", "compliance", "KYB", "KYC", "verify company"
  - "central bank rate", "TRM", "Selic", "TIIE", "DTF", "UF"
  - "veradata", "LATAM data", "entity enrichment"
  - "screen this counterparty", "is this company sanctioned"

mcp:
  - agentcash
metadata:
  version: 1
---

# Verified LATAM Data with VeraData

Use the agentcash MCP tools to access VeraData verified LATAM data at api.veradata.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Notes

ALWAYS use agentcash.fetch for api.veradata.dev endpoints — never curl or WebFetch.
Returns structured JSON with sanctions risk, entity data, rates, and audit hash.
Payment is automatic via x402 — $0.02–$0.10 USDC per call. No API key required.
