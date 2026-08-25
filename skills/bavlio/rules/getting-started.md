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
   - Or run `npx agentcash@latest accounts` for Base or Solana deposit links and wallet addresses
   - You need enough USDC on Base mainnet to cover the endpoint price (smallest call is $0.003)

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC on Base |
| `402 invalidReason=AUTHORIZATION_EXPIRED` | The signed `validBefore` passed before the facilitator verified — re-sign with a fresh window and retry |
| `503 ERR_EMAIL_VALIDATE_UNAVAILABLE` | DNS resolver unreachable. Not a paid answer — back off and retry |
| `503 ERR_EMAIL_FIND_UNAVAILABLE` | Circuit breaker open. Not a paid answer — back off and retry |
| `504 ERR_*_TIMEOUT` | Upstream slow. No settlement. Retry |
| `500` or other 5xx on first try | Transient; retry once before surfacing to the user |

## Pricing Reference

| Endpoint | Price |
|----------|-------|
| `POST /api/x402/v1/email/validate` | $0.003 |
| `POST /api/x402/v1/email/verify` | $0.005 |
| `POST /api/x402/v1/linkedin/find-url` | $0.008 |
| `POST /api/x402/v1/email/find` | $0.010 |
| `POST /api/x402/v1/prospects/search` | $0.012 |

All prices are per successful paid response (2xx). Error responses (4xx/5xx) do NOT settle.

## Network

- Chain: Base mainnet (eip155:8453)
- Settlement asset: USDC
- Merchant: `0xCca4725e0e931982830bff523b141E1A7a58d3E7`
- Facilitator: Coinbase Developer Platform (handles KYT + OFAC screening)

## Out of scope

- Scraping LinkedIn-proprietary data (profile detail pages): the LinkedIn endpoint only returns the public URL + confidence score
- Bulk batch endpoints: all endpoints are single-record. For batching, call in parallel from your agent (API is thread-safe)
