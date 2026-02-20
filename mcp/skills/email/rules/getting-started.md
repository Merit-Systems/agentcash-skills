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
| "DNS pending" after subdomain purchase | Wait ~5 minutes for DNS verification |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |
| "400 Bad Request" | Verify parameter names match exactly from examples in SKILL.md |

## Pricing Reference

| Endpoint | Price |
|----------|-------|
| Send (shared) | $0.02 |
| Send (subdomain/inbox) | $0.005 |
| Buy inbox (30 days) | $1.00 |
| Top up 30d / 90d / 365d | $1.00 / $2.50 / $8.00 |
| Buy subdomain | $5.00 |
| Create subdomain inbox | $0.25 |
| List/read messages | $0.001 |
