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

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC — calls cost $0.54 each |
| "Payment failed" | Check balance, retry (transient errors) |
| Call not completing | Poll `npx agentcash fetch https://stablephone.dev/api/call/{call_id}` every 5-10s until `completed: true` |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |
| Invalid phone number | Use E.164 format: +1XXXXXXXXXX for US numbers |

## Pricing Reference

| Endpoint | Price |
|----------|-------|
| Make a call | $0.54 |
| Buy phone number | $20.00 |
| Top up number (30 days) | $15.00 |
| Check status / list numbers | Free |
