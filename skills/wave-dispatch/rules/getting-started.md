# Getting Started with WAVE Dispatch

WAVE Dispatch is a paid AI routing service — agents pay per call via x402/MPP. Your wallet handles settlement automatically via `npx agentcash@latest fetch`.

## Install

Prerequisite: `agentcash` CLI (already a dependency of this skill bundle).

```bash
npx skills add Merit-Systems/agentcash-skills
```

Or use the agentcash CLI directly if you've already onboarded a wallet.

## Wallet setup

If you don't have an agentcash wallet yet:

```bash
npx agentcash@latest wallet create
```

Fund the wallet with at least a few cents of USDC on Base (any x402-priced WAVE Dispatch call costs ≤ $0.001).

## First call — classify a prompt (cheapest)

`/classify` runs the edge classifier without executing the prompt. Useful for cost preview.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/classify -m POST -b '{
  "prompt": "What is 2+2?"
}'
```

Expected output: `{"route": "edge-fast", "probability": 0.99, ...}` — answered for $0.0001.

## First execution — route + execute

```bash
npx agentcash@latest fetch https://dispatch.wave.online/ -m POST -b '{
  "prompt": "Explain RAII in C++ in two sentences."
}'
```

The classifier picks the cheapest capable model; Dispatch runs it; you get the answer plus signed `Payment-Receipt` in the response headers.

## Discover the API live

```bash
# Full OpenAPI 3.1 spec
npx agentcash@latest fetch https://dispatch.wave.online/openapi.json

# MPP service info (canonical agent-discovery endpoint)
npx agentcash@latest fetch https://dispatch.wave.online/v1/mpp/discovery

# Per-rail status
npx agentcash@latest fetch https://dispatch.wave.online/status
```

## Verify a Payment-Receipt

Every 200 from a priced endpoint includes a `Payment-Receipt: <jws>` header signed against WAVE's platform DID:

```bash
# Fetch the platform DID document
curl https://dispatch.wave.online/.well-known/did.json

# The JWS payload contains: {amount, currency, recipient, tx_hash, route, model, timestamp}
# Verify with the public key from did.json
```

## Troubleshooting

- **402 loop:** wallet has insufficient USDC. Check balance + chain.
- **400 bad input:** run `agentcash check <url>` first to see the exact schema for the endpoint.
- **429 rate limited:** per-key + per-IP rate limits exist; back off and retry.
- **Unexpected route:** the classifier is conservative — if the prompt looks frontier-class, it'll escalate. That's the safety floor.

## Where to go next

- Read the full WAVE Dispatch protocol guide: https://dev.wave.online/mpp
- Threat model: https://github.com/wave-av/wave-dispatch/blob/master/docs/threat-model.md
- Source: https://github.com/wave-av/dispatch-edge (open-core mirror)
