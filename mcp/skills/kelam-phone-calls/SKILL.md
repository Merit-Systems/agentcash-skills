---
name: kelam-phone-calls
description: |
  Real outbound AI phone calls in the most realistic voices, and your own inbound/outbound
  voice agents, via x402 at call.kelam.sh -- a fraction of what managed voice-AI platforms cost.

  USE FOR:
  - Placing a real outbound phone call to accomplish a task (book, confirm, chase, ask) and getting the transcript + outcome back
  - Extracting typed fields from a call (a quoted price, a yes/no, a date)
  - Provisioning your own inbound or outbound voice agent under your wallet
  - Topping up prepaid credits so an inbound agent can answer
  - Designing/editing an agent in plain English with the builder

  TRIGGERS:
  - "call", "phone call", "make a call", "dial", "voicemail"
  - "voice agent", "receptionist", "answer my phone", "book by phone"
  - "transcript", "call outcome", "call summary"

  ALWAYS use agentcash.fetch for call.kelam.sh endpoints.
mcp:
  - agentcash
metadata:
  version: 1
---

# Real AI Phone Calls with Kelam

Place real phone calls and provision voice agents via x402 payments at `https://call.kelam.sh`.
The most realistic voices, billed to the second, a fraction of what managed voice-AI platforms cost.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for CLI install and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Place an outbound call | `POST https://call.kelam.sh/api/place_call` | $0.05 + $0.15/min (capped $2) |
| Check call status | `GET https://call.kelam.sh/api/call_status?call_id=...` | Free |
| Create a voice agent | `POST https://call.kelam.sh/api/create_agent` | $2.00 |
| Top up inbound credits | `POST https://call.kelam.sh/api/top_up` | $0.40 per call |
| Talk to the agent builder | `POST https://call.kelam.sh/api/build` | $0.40 per message |
| Allowlist a caller | `POST https://call.kelam.sh/api/allowlist_number` | Free |
| List your agents | `GET https://call.kelam.sh/api/list_agents` | Free |
| Credit balance | `GET https://call.kelam.sh/api/balance` | Free |
| Builder transcript | `GET https://call.kelam.sh/api/build_view?session_id=...` | Free |

An outbound call is metered by talk time ($0.05 connect + $0.15/min, billed to the second, capped at $2.00). A call that never reaches anyone is free.

## Place a Call

```
agentcash.fetch(
  url="https://call.kelam.sh/api/place_call",
  method="POST",
  body={
    "to": "+14155551234",
    "task": "Call this restaurant and book a table for 4 tonight at 7pm under the name Sami. Confirm the exact time.",
    "extract": [{"name": "booked", "type": "boolean", "description": "did they confirm the reservation"}]
  }
)
```

Returns `{ call_id, status, answered_by, duration_seconds, summary, outcome, transcript, recording_url, price_usd }`.
A call reaching no one (busy / no answer) is free.

## Create a Voice Agent

```
agentcash.fetch(
  url="https://call.kelam.sh/api/create_agent",
  method="POST",
  body={"name": "front-desk", "instructions": "You are a friendly dental receptionist. Book appointments and answer questions."}
)
```

Returns `{ agent_id, phone_number, inbound }`. Requires wallet auth (SIWX).

IMPORTANT: an inbound agent answers a caller only after you BOTH fund it (`top_up`) AND allowlist
that caller (`allowlist_number`, or `allow_callers` at create time). Until both are done it
silently rejects every inbound call.

## Talk to the Builder

Design or edit an agent in plain English. Omit `session_id` to start (the reply carries one);
pass it back to continue. Replies are read for free with `build_view`.

```
agentcash.fetch(
  url="https://call.kelam.sh/api/build",
  method="POST",
  body={"message": "Build me a receptionist for a dentist that books appointments and takes messages."}
)
```
