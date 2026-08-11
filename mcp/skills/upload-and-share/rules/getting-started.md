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
| Upload URL expired | Buy a new slot — see `uploadUrlExpiresAt`; extend up to 24h via `policyTtlSeconds` |
| curl fails | Use `--data-binary` and absolute file path |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |

## Pricing Reference

| Tier | Max Size | Retention | Price |
|------|----------|-----------|-------|
| 10mb | 10 MB | 6 months | $0.02 |
| 100mb | 100 MB | 6 months | $0.20 |
| 1gb | 1 GB | 6 months | $2.00 |
| short-10mb | 10 MB | 7 days | $0.005 |
| short-100mb | 100 MB | 7 days | $0.02 |
| short-1gb | 1 GB | 7 days | $0.10 |
| short-5gb | 5 GB | 7 days | $0.50 |

`short-5gb` is for file uploads only (not sites).
