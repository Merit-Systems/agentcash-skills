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

4. **See the routes and prices:**
   ```bash
   npx agentcash@latest discover https://docs.tanod.dev
   ```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC |
| "405 Method Not Allowed" | Use the exact path from the Quick Reference table; every route is POST |
| "400" or "422" | Check the parameter names in SKILL.md; one of `url` or the base64 file is required |
| A private or internal URL is refused | Pass the file as base64 instead |
