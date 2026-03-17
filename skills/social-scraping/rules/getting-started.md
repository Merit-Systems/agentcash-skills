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

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC |
| "Payment failed" | Check balance, retry (transient errors) |
| "status: pending" | Keep polling `npx agentcash@latest fetch https://stablesocial.dev/api/jobs?token=...` every 3-5 seconds |
| "status: failed" | Data collection failed — you are NOT charged. Try again or check input |
| "401 on /api/jobs" | Token expired (30 min TTL). Make a new paid POST request |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |

## Pricing Reference

All StableSocial endpoints cost $0.06 per call (paid POST trigger). Polling is free.
