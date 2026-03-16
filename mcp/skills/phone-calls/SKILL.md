---
name: phone-calls
description: |
  Make AI phone calls, buy phone numbers, and get call transcripts via x402.

  USE FOR:
  - Making AI-powered outbound phone calls
  - Scheduling calls and getting transcripts
  - Buying and managing phone numbers
  - Leaving voicemails
  - Transferring calls to humans

  TRIGGERS:
  - "call", "phone call", "make a call", "dial"
  - "buy number", "phone number", "get a number"
  - "voicemail", "leave a message"
  - "transcript", "call recording", "call summary"
  - "AI call", "automated call", "bland"

  ALWAYS use agentcash.fetch for stablephone.dev endpoints.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
mcp:
  - agentcash
---

# AI Phone Calls with StablePhone

Make AI-powered phone calls, buy phone numbers, and get transcripts via x402 payments at `https://stablephone.dev`.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Make a call | `https://stablephone.dev/api/call` | $0.54 |
| Check call status | `GET https://stablephone.dev/api/call/{call_id}` | Free |
| Buy phone number | `https://stablephone.dev/api/number` | $20.00 |
| Top up number (30d) | `https://stablephone.dev/api/number/topup` | $15.00 |
| List your numbers | `GET https://stablephone.dev/api/numbers?wallet=0x...` | Free |

## Make a Call

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call",
  method="POST",
  body={
    "phone_number": "+14155551234",
    "task": "Call this person and ask if they're available for a meeting tomorrow at 2pm. Be polite and professional."
  }
)
```

**Required:**
- `phone_number` — E.164 format (e.g. `+14155551234`)
- `task` — instructions for the AI agent (what to say, how to behave)

**Optional:**
- `from` — outbound caller ID (must be an active StablePhone number)
- `first_sentence` — specific opening line
- `voice` — voice preset (see Voice Options below)
- `max_duration` — max call length in minutes (1-30, default 5)
- `wait_for_greeting` — wait for recipient to speak first (default false)
- `record` — record call audio (default true)
- `model` — `"base"` (default, best quality) or `"turbo"` (lowest latency)
- `transfer_phone_number` — number to transfer to if caller requests a human
- `voicemail_action` — `"hangup"` (default), `"leave_message"`, or `"ignore"`
- `voicemail_message` — message to leave (required when action is `"leave_message"`)
- `metadata` — custom key-value data to attach

## Check Call Status

Poll until `completed` is true:

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call/{call_id}",
  method="GET"
)
```

**Response fields:**
- `status` — call status
- `completed` — boolean, true when call is done
- `answered_by` — `"human"`, `"voicemail"`, or `"no_answer"`
- `call_length` — duration in seconds
- `summary` — AI-generated call summary
- `transcript` — full text transcript
- `transcripts` — array of timestamped transcript segments
- `recording_url` — URL to call recording
- `price` — actual cost

Poll every 5-10 seconds. Calls typically complete in 1-5 minutes depending on `max_duration`.

## Voice Options

| Voice | Description |
|-------|-------------|
| `nat` | American female (default) |
| `josh` | Articulate American male |
| `maya` | Young American female, soft |
| `june` | American female |
| `paige` | Calm, soft-tone female |
| `derek` | Soft and engaging male |
| `florian` | German male |

## Buy a Phone Number

Buy a number to use as outbound caller ID:

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/number",
  method="POST",
  body={
    "area_code": "415",
    "country_code": "US"
  }
)
```

**Optional:**
- `area_code` — 3-digit US/CA area code (random if omitted)
- `country_code` — `"US"` (default) or `"CA"`

Numbers expire after 30 days. Top up to extend.

## Top Up a Number

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/number/topup",
  method="POST",
  body={"phone_number": "+14155551234"}
)
```

Extends by 30 days from current expiry. Top-ups stack. Anyone can top up any number.

## List Your Numbers

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/numbers?wallet=YOUR_WALLET_ADDRESS",
  method="GET"
)
```

## Workflows

### Quick Call

- [ ] (Optional) Check balance: `agentcash.get_balance`
- [ ] Make call with task instructions
- [ ] Poll status until completed
- [ ] Review transcript and summary

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call",
  method="POST",
  body={
    "phone_number": "+14155551234",
    "task": "Ask if the business is open on weekends and what their hours are."
  }
)
```

### Leave a Voicemail

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call",
  method="POST",
  body={
    "phone_number": "+14155551234",
    "task": "Leave a voicemail about the appointment.",
    "voicemail_action": "leave_message",
    "voicemail_message": "Hi, this is a reminder about your appointment tomorrow at 3pm. Please call back to confirm."
  }
)
```

For natural voicemail delivery, use `"voicemail_action": "ignore"` which bypasses detection and lets the AI speak naturally into the voicemail system.

### Call with Transfer

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call",
  method="POST",
  body={
    "phone_number": "+14155551234",
    "task": "Qualify this lead. Ask about their budget and timeline. If they're interested, offer to transfer to a sales rep.",
    "transfer_phone_number": "+14155559999",
    "voice": "josh",
    "max_duration": 10
  }
)
```

### Set Up Dedicated Number

- [ ] Buy a phone number ($20)
- [ ] Use it as caller ID for outbound calls
- [ ] Top up before expiry ($15/30 days)

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/number",
  method="POST",
  body={"area_code": "212", "country_code": "US"}
)
```

Then use it as `from`:

```mcp
agentcash.fetch(
  url="https://stablephone.dev/api/call",
  method="POST",
  body={
    "phone_number": "+14155551234",
    "from": "+12125551234",
    "task": "Confirm the reservation for Friday at 7pm."
  }
)
```

## Cost Estimation

| Task | Cost |
|------|------|
| Single call | $0.54 |
| Call + dedicated number | $20.54 |
| Monthly number maintenance | $15.00 |
| 10 calls/month + number | $20.00 + $5.40 = $25.40 first month |
