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

4. **Or start free:** the first 100 checks per client are free — send
   header `X-Use-Free-Allowance: yes` with no payment:
   ```bash
   npx agentcash@latest fetch https://scrapecheck.fly.dev/verify -m POST \
     -H "X-Use-Free-Allowance: yes" -b '{ "url": "...", "claim": { "price": "..." }, "asked": "..." }'
   ```
   Past the allowance, calls pay $0.01 (verify) / $0.002 (presence) in
   USDC via x402 — no account, no API key.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC, or use the free-allowance header |
| Verdict is `unverifiable` | The page is JS-only or the claim could not be confirmed — this is a real answer, never converted to a pass |
| 400 response | Body must be `{ url, claim: {field: value}, asked }` — all three required |
