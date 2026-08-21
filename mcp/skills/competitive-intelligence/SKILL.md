---
name: competitive-intelligence
description: |
  Competitive intelligence analysis for AI agents using Intelica — pay-per-call via x402 ($0.05 USDC).
  Returns structured go/no-go decisions with IMI score, competitor mapping, and execution plan.

  USE FOR:
  - Analyzing market entry opportunities before committing resources
  - Screening startup ideas or investment targets
  - Mapping competitors and their structural moats
  - Getting go/no-go decisions: enter/avoid/monitor/acquire/partner
  - Assessing regulatory compliance requirements for new markets
  - Building sales intelligence on competitive positioning

  TRIGGERS:
  - "analyze", "competitive", "market entry", "should I build", "is there a market"
  - "competitor", "moat", "market opportunity", "venture screening"
  - "regulatory", "compliance", "risk assessment", "sales enablement"
  - "IMI", "intelica", "competitive intelligence"
  - "enter this market", "avoid this", "monitor this company"

mcp:
  - agentcash
metadata:
  version: 1
---

# Competitive Intelligence with Intelica

Use the agentcash MCP tools to access Intelica competitive intelligence at api.intelica.dev.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Notes

ALWAYS use agentcash.fetch for api.intelica.dev endpoints — never curl or WebFetch.
Returns structured JSON with IMI score, decision, competitors, and execution plan.
Payment is automatic via x402 — $0.05 USDC per standard analysis.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| Competitive analysis | `https://api.intelica.dev/intel` | $0.05 | Market entry, competitor mapping |
| Venture screening | `https://api.intelica.dev/intel` (mode: venture_screening) | $1.00 | Startup/investment evaluation |
| Market entry execution | `https://api.intelica.dev/intel` (mode: market_entry_execution) | $1.00 | Detailed GTM roadmap |
| Regulatory compliance | `https://api.intelica.dev/intel` (mode: regulatory_compliance) | $1.00 | Compliance requirements by market |
| Risk assessment | `https://api.intelica.dev/intel` (mode: risk_assessment) | $1.00 | Risk scoring and mitigation |
| Sales enablement | `https://api.intelica.dev/intel` (mode: sales_enablement) | $1.00 | Competitive battlecards |
| Batch analysis | `https://api.intelica.dev/batch` | $0.20 | Up to 10 companies in parallel |
| Market saturation | `https://api.intelica.dev/graph/market-share/{sector}` | FREE | Sector overview (no payment needed) |

## Workflows

### Standard Competitive Analysis

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/intel",
  method="POST",
  body={
    "text": "Stripe competitor for developers in Southeast Asia with local payment methods",
    "mode": "competitive"
  }
)
```

**Returns:**
- `decision_recommendation.action` — `enter` / `avoid` / `monitor` / `acquire` / `partner`
- `decision_recommendation.confidence_score` — 0.0-1.0 (data richness)
- `decision_recommendation.rationale` — specific data-anchored reason
- `intelica_moat_index` — 0.0-1.0 structural moat strength
- `detected_competitors` — list of real competitors found
- `execution_plan.non_obvious_insight` — the insight that changes everything
- `execution_plan.week_1_actions` — immediate next steps
- `execution_plan.budget_guidance` — phase-by-phase cost estimates

### Venture Screening (Investment Evaluation)

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/intel",
  method="POST",
  body={
    "text": "AI-native accounting software for SMBs replacing QuickBooks with autonomous bookkeeping",
    "mode": "venture_screening"
  }
)
```

### Batch Analysis (Multiple Companies)

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/batch",
  method="POST",
  body={
    "items": [
      {"text": "Stripe competitive positioning", "mode": "competitive"},
      {"text": "Adyen market strategy in LATAM", "mode": "competitive"},
      {"text": "2C2P payment infrastructure SEA", "mode": "competitive"}
    ]
  }
)
```

### Free Market Saturation Check (No Payment)

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/graph/market-share/fintech",
  method="GET"
)
```

Available sectors: `ai`, `fintech`, `healthcare`, `saas`, `defense`, `logistics`, `legaltech`, `edtech`, `robotics`, `energy`, `ecommerce`

### Auto-Monitor (Pulse Alerts)

Add `auto_execute: true` and `webhook_url` to get automatic alerts when the IMI changes:

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/intel",
  method="POST",
  body={
    "text": "Klue competitive intelligence platform",
    "mode": "competitive",
    "auto_execute": True,
    "webhook_url": "https://your-agent.com/alerts"
  }
)
```

If `action=monitor`, Intelica auto-subscribes to IMI change alerts at $0.01 USDC/alert.

## Understanding the Output

### IMI Score Interpretation

| IMI Range | Meaning | Recommended Action |
|-----------|---------|-------------------|
| 0.0 – 0.65 | Weak moat — market is contestable | `enter` |
| 0.65 – 0.80 | Medium moat — selective entry possible | `monitor` |
| 0.80 – 1.0 | Strong moat — entrenched incumbents | `avoid` |

### Action Meanings

- **enter** — Clear opportunity, proceed with GTM
- **avoid** — Moat too strong, pivot or find adjacent angle
- **monitor** — Wait for moat to weaken before committing
- **acquire** — Best path is buying an incumbent
- **partner** — Build on top of the moat, not against it

## Cost Optimization

### Use Free Market Overview First

Before paying for `/intel`, get a free sector overview:

1. `GET /graph/market-share/{sector}` — FREE sector saturation
2. Review top companies and IMI distribution
3. Only then call `/intel` for specific company analysis

### Batch for Multiple Companies

Use `/batch` ($0.20) instead of multiple `/intel` calls ($0.05 each) when analyzing 4+ companies:

- 4 companies via `/intel` = $0.20
- 4 companies via `/batch` = $0.20 (same price, parallel execution)
- 10 companies via `/batch` = $0.20 (much cheaper than $0.50)

## API Details

- **Base URL:** `https://api.intelica.dev`
- **Auth:** x402 automatic payment via AgentCash wallet
- **Networks:** Base (`eip155:8453`) + Solana
- **Competitive Graph:** 3,600+ companies with IMI scores
- **OpenAPI:** `https://api.intelica.dev/openapi.json`
- **Docs:** `https://api.intelica.dev/llms-full.txt`
