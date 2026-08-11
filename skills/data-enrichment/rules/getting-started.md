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
| "No match found" | Try different identifiers (email vs LinkedIn) or use search first |
| "405 Method Not Allowed" | Verify endpoint path matches exactly from Quick Reference table in SKILL.md |
| "400 Bad Request" | Verify parameter names match exactly from examples in SKILL.md |

## Pricing Reference

| Endpoint | Price |
|----------|-------|
| fullenrich/people-search | $0.15 (if results) |
| fullenrich/company-search | $0.15 (if results) |
| pdl/people-enrich | $0.28 (if match) |
| companyenrich/org-enrich | $0.06 |
| companyenrich/properties-enrich | $0.06 |
| clado/contacts-enrich | $0.20 |
| hunter/email-verifier | $0.03 |
| hunter/email-verifier/jobs/{jobId} | Free (SIWX) |
| minerva/resolve | $0.02 |
| minerva/enrich | $0.05 |
| minerva/validate-emails | $0.01 |
