# Getting Started

## Setup

1. **Install the agentcash CLI:**
   ```bash
   npm install -g agentcash
   ```

2. **Check wallet:**
   ```bash
   npx agentcash wallet info
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `npx agentcash wallet redeem YOUR_CODE`
   - Or send USDC on Base to your wallet address
   - Or use the deposit UI: `https://x402scan.com/mcp/deposit/<your-wallet-address>`

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
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
