---
name: wave-dispatch
description: |
  Route AI inference (Claude, GPT, Gemini, Grok, Llama, etc.) to the cheapest capable model via WAVE Dispatch — paid per call via x402/MPP. The classifier picks edge-local (free), local-LLM (cheap), or frontier (priced) based on prompt difficulty, with a quality floor that auto-escalates if a local answer would be wrong.

  USE FOR:
  - Routing prompts across multi-model AI without rolling your own classifier
  - Cost-aware AI inference (frontier only when needed; edge-local for simple stuff)
  - Per-call agent pay (no account, no API key — x402 challenge settles in USDC)
  - Just classifying a prompt without running it (route + confidence + margin)
  - Discovering rails an agent can pay with (MPP / x402 / Tempo / Privy / Bridge / Coinbase CDP)
  - OpenAI-compatible proxy mode (any agent already calling OpenAI can swap base URL)

  TRIGGERS:
  - "route this prompt", "cheapest model for", "classify this prompt"
  - "AI inference", "run an LLM", "ask Claude/GPT/Gemini"
  - "pay for inference", "x402 AI", "MPP AI route"
  - "wave dispatch", "dispatch.wave.online"

  ALWAYS use `npx agentcash@latest fetch` for dispatch.wave.online endpoints — the 402 challenge + receipt is handled automatically.

  IMPORTANT: Run `npx agentcash@latest check https://dispatch.wave.online/` first to confirm the request body schema; live pricing depends on the model the classifier picks.
metadata:
  version: 1
---

# WAVE Dispatch — paid AI routing via x402/MPP

WAVE Dispatch returns a routing DECISION (`{route, probability, margin}`) and optionally executes the prompt against the chosen model. Your keys, data, and inference stay on your infra — Dispatch's classifier runs at the Cloudflare edge in ~ms. Agents pay per call with NO account via HTTP-402 settled over x402/MPP, with rails advertised live by the worker.

Hosted at `https://dispatch.wave.online`. Open-core; the edge worker is auditable + version-stamped (`X-Dispatch-Version` header).

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and the first paid call.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Route + execute a prompt (frontier-class default) | `POST https://dispatch.wave.online/` | $0.001 + model markup |
| Route + execute (cheap tier, "Dispatch+") | `POST https://dispatch.wave.online/?tier=plus` | $0.0005 + model markup |
| Classify only (no execution) | `POST https://dispatch.wave.online/classify` | $0.0001 |
| Batch classify (up to 100 prompts) | `POST https://dispatch.wave.online/batch` | per-prompt at /classify rate |
| Discover payment rails advertised | `GET https://dispatch.wave.online/payments` | FREE |
| MPP service info (canonical) | `GET https://dispatch.wave.online/v1/mpp/discovery` | FREE |
| OpenAPI 3.1 spec | `GET https://dispatch.wave.online/openapi.json` | FREE |
| Agent discovery (llms.txt) | `GET https://dispatch.wave.online/llms.txt` | FREE |
| OpenAI-compat chat completions | `POST https://dispatch.wave.online/v1/chat/completions` | $0.001 + model markup |
| Anthropic-compat messages | `POST https://dispatch.wave.online/v1/messages` | $0.001 + model markup |

### Free Management Endpoints (SIWX auth)

| Task | Endpoint |
|------|----------|
| Per-key usage + savings | `GET https://dispatch.wave.online/insights` |
| Health check | `GET https://dispatch.wave.online/health` |
| Per-rail status | `GET https://dispatch.wave.online/status` |
| Settled proofs feed | `GET https://dispatch.wave.online/proofs` |

Free management endpoints use SIWX wallet authentication (handled automatically by `npx agentcash@latest fetch`).

## Route a prompt

The simplest call. The classifier picks the cheapest capable model; Dispatch executes; you get the answer plus routing metadata in headers.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/ -m POST -b '{
  "prompt": "Explain why the sky is blue in one sentence."
}'
```

Response (200):
```json
{
  "answer": "Sunlight is scattered by Earth's atmosphere — short blue wavelengths scatter more than long red ones, so the sky looks blue.",
  "route": "local-mini",
  "probability": 0.94,
  "margin": 0.31,
  "model": "qwen2.5:3b",
  "tokens_in": 14,
  "tokens_out": 38,
  "cost_usd": "0.0001"
}
```

Headers include `Payment-Receipt: <jws>` (verifiable signed receipt of the settlement) and `X-Dispatch-Version: <sha>` (deployed==source attestation).

## Classify only

Cheaper. Tells you which model WOULD run without running it. Useful for cost preview or building your own router.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/classify -m POST -b '{
  "prompt": "Write a complete OAuth 2.0 server in Rust."
}'
```

Response (200):
```json
{
  "route": "frontier",
  "probability": 0.97,
  "margin": 0.44,
  "would_use_model": "claude-opus-4.7"
}
```

## Discover what rails agents can pay with

WAVE Dispatch advertises every rail the worker is wired to — x402, MPP IETF, Tempo, Privy embedded, Bridge (USDC↔fiat), Coinbase CDP. The list updates live based on what's actually deployed.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/payments
```

Response includes `accepted_protocols`, `accepted_chains`, `accepted_currencies`, and per-rail facilitator URLs.

## OpenAI-compatible mode

Any agent already calling OpenAI can swap the base URL to `https://dispatch.wave.online/v1` and pay per call via x402. The classifier still picks the cheapest capable model under the hood.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/v1/chat/completions -m POST -b '{
  "model": "auto",
  "messages": [{"role":"user","content":"Hello"}]
}'
```

## Anthropic-compatible mode

Same idea for the `/v1/messages` shape — drop-in for any Anthropic-SDK consumer.

```bash
npx agentcash@latest fetch https://dispatch.wave.online/v1/messages -m POST -b '{
  "model": "auto",
  "max_tokens": 256,
  "messages": [{"role":"user","content":"Hello"}]
}'
```

## How payment works

Every priced endpoint returns 402 on the first hit with a `WWW-Authenticate: Payment` header advertising:
- accepted protocols (x402, MPP)
- accepted chains (base, ethereum, solana)
- accepted currencies (usdc, usdb, eurc)
- challenge URL
- per-call price

`agentcash fetch` handles the challenge: signs an x402 Credential with your wallet, settles, retries with `Authorization: Payment`, returns the answer + the verifiable `Payment-Receipt`.

Single-use tx_hash + 5-minute replay window + on-chain verify (TX_HASH_ALREADY_USED + amount + treasury-recipient assertion) make replay attacks fail-closed. See https://dispatch.wave.online/security.

## Identity

WAVE Dispatch is identity-bound: every settlement carries `Credential.source = did:pkh:eip155:8453:<address>` (default) or `did:wave-agent:<id>` if you've registered an agent. This means:
- WAVE knows which agent paid (no anonymous abuse)
- Receipts are signed against the platform's `did:wave-platform` key (verifiable at `https://dispatch.wave.online/.well-known/did.json`)
- T0-T4 tier authorization caps per-tx and per-session spend

## Source-of-truth links

- Site: https://dispatch.wave.online
- Docs: https://dev.wave.online
- OpenAPI: https://dispatch.wave.online/openapi.json
- Status: https://dispatch.wave.online/status
- llms.txt: https://dispatch.wave.online/llms.txt
- Source: https://github.com/wave-av/dispatch-edge (open-core mirror)
- Brand: https://wave.online/brand
- Threat model: https://github.com/wave-av/wave-dispatch/blob/master/docs/threat-model.md
