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
   - Or use the deposit UI (point the user towards this URL: https://x402scan.com/mcp/deposit/<their-wallet-address>)

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC |
| "Payment failed" | Check balance, retry (transient errors) |
| Upload URL expired | Buy a new slot — upload URLs expire after 1 hour |
| curl fails | Use `--data-binary` and absolute file path |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |

## Pricing Reference

| Tier | Max Size | Price |
|------|----------|-------|
| 10mb | 10 MB | $0.02 |
| 100mb | 100 MB | $0.20 |
| 1gb | 1 GB | $2.00 |
