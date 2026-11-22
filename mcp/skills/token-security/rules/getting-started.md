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

4. **Verify NullClear endpoints:**
```mcp
   agentcash.discover_api_endpoints(url="https://api.nullclear.dev")
   agentcash.check_endpoint_schema(url="https://api.nullclear.dev/v1/bazaar/token-security")
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient errors) |
| "400 Invalid token address" | Pass a valid 42-char `0x...` EVM address or base58 Solana mint |
| "405 Method Not Allowed" | Verify exact `/v1/bazaar/*` path from Quick Reference in SKILL.md |

## Pricing Reference

| Endpoint | Method | Price |
|----------|--------|-------|
| `/v1/bazaar/token-security` | GET / POST | $0.005 |
| `/v1/bazaar/token-verdict` | GET / POST | $0.005 |
| `/v1/bazaar/guard` | POST / GET | $0.005 |
| `/v1/bazaar/scan-batch` | POST | $0.025 (up to 15 tokens) |
| `/v1/bazaar/due-diligence` | POST / GET | $0.10 |
