# Getting Started

## Setup

1. **Install the agentcash MCP:**
   ```bash
   npx agentcash@latest install --client claude-code -y
   ```

2. **Check wallet:**
```mcp
   agentcash.get_balance()
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `agentcash.redeem_invite(code="YOUR_CODE")`
   - Or call `agentcash.list_accounts()` to get Base or Solana deposit links and wallet addresses

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC |
| "Payment failed" | Check balance, retry (transient errors) |
| "status: pending" | Keep polling GET /api/jobs/{jobId} (or the legacy GET /api/jobs?token=...) every 3-5 seconds |
| "status: failed" | Data collection failed — you are NOT charged. Try again or check input |
| "401 on /api/jobs" | Legacy token expired (30 min TTL) — poll by jobId instead, or make a new paid POST. Durable job polling requires SIWX auth from the paying wallet (`agentcash.fetch` signs automatically) |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |

## Pricing Reference

All StableSocial endpoints cost $0.06 per call (paid POST trigger) — legacy platform endpoints, the Scrape Creators suite (`/api/sc/*`), and Lightreel (`/api/lightreel/*`) alike. Polling is free.
