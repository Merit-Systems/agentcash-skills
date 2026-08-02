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

   These endpoints settle on **Base mainnet in USDC**. Settlement is gasless for
   the buyer (EIP-3009), so a USDC balance is enough — no ETH required.

## Check the price without paying

An unpaid request returns the challenge, so you can confirm the price and terms
before spending anything:

```bash
curl -i "https://x402-mcp.onrender.com/base/tx-decision"
```

Expect `402` with a `PAYMENT-REQUIRED` header. Decoding it shows network
`eip155:8453`, USDC, and `10000` atomic units ($0.01).

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient facilitator errors are safe to retry — nothing settles on a failed attempt) |
| `422` response | A malformed parameter; nothing was charged |
| Stale-looking numbers | Check `as_of_block` in the response; the snapshot is cached ~4s |
