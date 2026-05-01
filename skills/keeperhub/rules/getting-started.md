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

KeeperHub workflows settle on **Base mainnet (USDC)**. Make sure the wallet has Base USDC before calling paid workflows.

## Pricing reference

Most read-type DeFi workflows are $0.01. Tipping and write-type workflows vary — always discover before calling:

```bash
npx agentcash@latest fetch https://app.keeperhub.com/api/mcp/workflows
```

The `/api/mcp/workflows` listing returns up-to-date pricing per slug.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient errors) |
| `404` from workflow slug | Re-list `/api/mcp/workflows` — catalog changes |
| `400 Bad Request` | Verify input schema at `/openapi.json` for that workflow's `requestBody` |
| "405 Method Not Allowed" | All workflow calls are POST. Use `-m POST`. |
