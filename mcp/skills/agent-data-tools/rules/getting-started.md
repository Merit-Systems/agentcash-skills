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

4. **Call a tool:**
   ```bash
   agentcash.fetch "https://agent-data-tools.revmesh2074.workers.dev/base-gas"
   ```

## How the calls work

- Every tool is a `GET` with query parameters. Unpaid, it answers `402 Payment Required` with x402 terms (USDC on Base, Polygon, Arbitrum or Solana) and an MPP challenge (Tempo first, then Base); agentcash pays and retries.
- One fixed price per successful call. A call that fails with 4xx or 5xx is never charged.
- agentcash `check` on the URL shows the exact input names and the price before you pay.
- The whole catalog: https://agent-data-tools.revmesh2074.workers.dev/openapi.json (566 tools) and https://agent-data-tools.revmesh2074.workers.dev/llms.txt.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC |
| `400 … is required` | A query parameter is missing; `check` the URL for its names |
| `502 target …` on `/extract` or `/pdf/text` | The URL you passed did not answer; nothing was charged |
| `503` on `/search`, `/news` or the AI tools | The upstream or the daily capacity is unavailable; nothing was charged, try later |
