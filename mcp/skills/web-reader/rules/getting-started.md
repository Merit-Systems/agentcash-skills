# Getting Started

## Setup

1. **Install the agentcash MCP:**
   ```bash
   npx agentcash@latest install --client claude-code
   ```

2. **Check wallet:**
   ```mcp
   agentcash.get_balance()
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `agentcash.redeem_invite(code="YOUR_CODE")`
   - Or call `agentcash.list_accounts()` to get Base or Solana deposit links and wallet addresses

4. **See the routes and prices:**
   ```mcp
   agentcash.discover_api_endpoints(url="https://web.tanod.dev")
   ```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install command, restart Claude Code |
| "Insufficient balance" | Fund wallet with USDC |
| "405 Method Not Allowed" | Use the exact path from the Quick Reference table; every route is POST |
| "400" or "422" | Check the parameter names in SKILL.md (`url`, `query`, `q` or `email`) |
| A private or internal URL is refused | Only public http(s) pages can be read |
