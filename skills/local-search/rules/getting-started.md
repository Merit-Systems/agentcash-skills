# Getting Started

## Setup

1. **Install the agentcash MCP:**
   ```bash
   npx agentcash@latest install --client claude-code
   ```

2. **Check wallet:**
   ```
   mcp__agentcash__get_wallet_info
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `mcp__agentcash__redeem_invite(code="YOUR_CODE")`
   - Or send USDC on Base to your wallet address

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC |
| "Payment failed" | Check balance, retry (transient errors) |
