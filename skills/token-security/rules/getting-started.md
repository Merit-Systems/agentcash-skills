# Getting Started

## Setup

1. **Install the agentcash CLI:**
   ```bash
   npm install -g agentcash
   ```

2. **Check wallet:**
   ```bash
   npx agentcash@latest balance
   ```

3. **Fund wallet** (if needed):
   - Redeem invite: `npx agentcash@latest redeem YOUR_CODE`
   - Or run `npx agentcash@latest accounts` to get Base or Solana deposit links and wallet addresses

4. **Verify NullClear endpoints:**
   ```bash
   npx agentcash@latest discover https://api.nullclear.dev
   npx agentcash@latest check https://api.nullclear.dev/v1/bazaar/token-security
   ```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
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
