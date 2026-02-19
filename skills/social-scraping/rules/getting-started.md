# Getting Started

## Setup

1. **Install the agentcash MCP:**
   ```bash
   npx agentcash@latest install --client claude-code -y
   ```

2. **Check wallet:**
```mcp
   agentcash.get_wallet_info
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `agentcash.redeem_invite(code="YOUR_CODE")`
   - Or send USDC on Base to your wallet address

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC |
| "Payment failed" | Check balance, retry (transient errors) |
| "status: pending" | Keep polling GET /api/jobs?token=... every 3-5 seconds |
| "status: failed" | Data collection failed — you are NOT charged. Try again or check input |
| "401 on /api/jobs" | Token expired (30 min TTL). Make a new paid POST request |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |

## Pricing Reference

All StableSocial endpoints cost $0.06 per call (paid POST trigger). Polling is free.
