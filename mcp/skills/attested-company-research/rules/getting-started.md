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
   - Or run `npx agentcash@latest accounts` for Base/Solana deposit links

4. **Add LION origin (one time):**
   ```bash
   npx agentcash@latest add https://lionx402.com
   ```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient facilitator errors) |
| "400 Bad Request" | Run `npx agentcash check '<url>'` first — confirm param names |
| Attestation verify fails | Use `?verify_helper=1` on any LION route for offline verifier JS |

## Pricing Reference (LION)

| Endpoint | Price |
|----------|-------|
| deep-research-json | $0.012 (1 merged call) |
| scrape-json | $0.004 |
| enrich-v1-json | $0.002/field (~$0.018 for full apollo_org) |
| compliance-bundle-json | $0.05 |
| token-risk-indicators-json | $0.01 |
| sec-financials-json | $0.005 |
| domain-intel-json | $0.005 |