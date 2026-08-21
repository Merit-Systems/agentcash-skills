# Getting Started with Intelica

## Installation

Intelica works automatically with AgentCash — no additional setup required.

Make sure your AgentCash wallet has USDC on Base:

```bash
npx agentcash balance
# Should show > $0.05 for a standard analysis
```

If balance is 0, fund your wallet:

```bash
npx agentcash accounts
# Send USDC (Base network) to the address shown
```

## Your First Analysis

```mcp
agentcash.fetch(
  url="https://api.intelica.dev/intel",
  method="POST",
  body={
    "text": "Stripe competitor for developers in Southeast Asia with local payment methods and lower cross-border fees",
    "mode": "competitive"
  }
)
```

AgentCash automatically pays $0.05 USDC when the API returns HTTP 402.

## Pricing Summary

| Mode | Price | Model |
|------|-------|-------|
| competitive | $0.05 | Claude Haiku |
| venture_screening | $1.00 | Claude Sonnet |
| market_entry_execution | $1.00 | Claude Sonnet |
| regulatory_compliance | $1.00 | Claude Sonnet |
| risk_assessment | $1.00 | Claude Sonnet |
| sales_enablement | $1.00 | Claude Sonnet |
| batch (up to 10) | $0.20 | Claude Haiku |
| graph/market-share | FREE | — |
