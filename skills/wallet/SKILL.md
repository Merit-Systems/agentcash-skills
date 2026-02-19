---
name: wallet
description: |
  Manage x402 wallet for paid API calls. Check balance, redeem invite codes, deposit USDC.

  USE FOR:
  - Checking wallet balance before API calls
  - Redeeming invite codes for free credits
  - Getting deposit address for USDC
  - Troubleshooting payment failures

  TRIGGERS:
  - "balance", "wallet", "funds", "credits"
  - "redeem", "invite code", "promo code"
  - "deposit", "add funds", "top up"
  - "insufficient balance", "payment failed"
---

# x402 Wallet Management

Your wallet is auto-created on first use and stored at `~/.agentcash/wallet.json`.

## Quick Reference

| Task | Tool | Notes |
|------|------|-------|
| Check balance | `mcp__agentcash__get_wallet_info` | Shows address + USDC balance |
| Redeem code | `mcp__agentcash__redeem_invite(code="...")` | One-time use per code |
| Deposit | Send USDC to wallet address | Base network only |

## Check Balance

```
mcp__agentcash__get_wallet_info
```

Returns:
- Wallet address (Base network)
- USDC balance
- Deposit link

Always check balance before expensive operations.

## Redeem Invite Code

```
mcp__agentcash__redeem_invite(code="YOUR_CODE")
```

- One-time use per code
- Credits added instantly
- Run `get_wallet_info` after to verify

## Deposit USDC

1. Get your wallet address: `mcp__agentcash__get_wallet_info`
2. Send USDC on **Base network** to that address
3. Funds available immediately after confirmation

**Important**: Only Base network USDC. Other networks or tokens will be lost.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Insufficient balance" | Check balance, deposit or redeem code |
| "Payment failed" | Transient error, retry the request |
| "Invalid invite code" | Code already used or doesn't exist |
| Balance not updating | Wait for Base network confirmation (~2 sec) |
