# Getting Started

## Setup

1. **Install the agentcash CLI:**
   ```bash
   npm install -g agentcash
   ```

2. **Check your wallet balance:**
   ```bash
   npx agentcash@latest balance
   ```

3. **Fund your wallet** (USDC on Base) if needed:
   - Redeem an invite: `npx agentcash@latest redeem YOUR_CODE`
   - Or run `npx agentcash@latest accounts` for Base/Solana deposit links + addresses

4. **Discover the endpoints:**
   ```bash
   npx agentcash@latest discover https://call.kelam.sh
   ```

## Inbound agents need TWO things

An agent you create with `inbound: true` answers a caller only when BOTH are true:

1. **It is funded** -- buy prepaid credits with `top_up` (each answered inbound call spends one).
2. **The caller is allowlisted** -- pass `allow_callers` at create time, or call `allowlist_number` later.

Until both are done, the agent silently rejects every inbound call.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund your wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient errors) |
| Inbound call not answered | Confirm the agent is funded (`top_up`) AND the caller is allowlisted |
| Wrong endpoint | Use the exact paths from the Quick Reference in SKILL.md |

## Pricing Reference

| Endpoint | Price |
|----------|-------|
| place_call | $0.05 connect + $0.15/min (billed to the second, capped $2.00; unconnected = free) |
| create_agent | $2.00 |
| top_up | $0.40 per prepaid inbound call |
| build | $0.40 per builder message |
| call_status / list_agents / balance / allowlist_number / build_view | Free |
