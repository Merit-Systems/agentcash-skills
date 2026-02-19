---
name: ai-phone-call
description: Make AI phone calls and buy phone numbers. $0.54 per call with transcripts, recordings, and summaries. Use when asked to call someone, make a phone call, have AI call a number, leave a voicemail, buy a phone number, or get a call transcript.
---

# StablePhone — AI Phone Calls

Pay-per-call AI phone calls. $0.54 per call.

## Make a Call

```bash
npx agentcash fetch https://stablephone.dev/api/call -m POST -b '{"phone_number": "+14155551234", "task": "Ask if they are available for a meeting tomorrow at 2pm."}'
```

Returns `{"success": true, "call_id": "abc-123"}`.

### Required Fields
- `phone_number` — E.164 format (e.g., `+14155551234`)
- `task` — Instructions for the AI agent

### Optional Fields
| Field | Default | Description |
|---|---|---|
| `from` | random | Outbound caller ID (must be an owned x402phone number) |
| `first_sentence` | auto | Specific opening line |
| `voice` | `nat` | Voice preset (see below) |
| `max_duration` | 5 | Max call length in minutes (1-30) |
| `wait_for_greeting` | false | Wait for recipient to speak first |
| `record` | true | Record call audio |
| `model` | `base` | `base` (best) or `turbo` (lowest latency) |
| `transfer_phone_number` | — | Number to transfer to if human requested |
| `voicemail_action` | `hangup` | `hangup`, `leave_message`, or `ignore` |
| `voicemail_message` | — | Message for voicemail (required with `leave_message`) |
| `metadata` | — | Custom key-value data |

**Voicemail tip:** Use `"voicemail_action": "ignore"` to bypass detection — best for leaving voicemails naturally.

## Check Call Status (free)

```bash
npx agentcash fetch https://stablephone.dev/api/call/CALL_ID
```

Poll until `completed: true`. Response includes: `status`, `summary`, `transcript`, `transcripts` (array), `recording_url`, `answered_by`, `call_length`, `price`.

## Phone Numbers

| Endpoint | Price | Purpose |
|---|---|---|
| `POST https://stablephone.dev/api/number` | $20.00 | Buy a number (30 days) |
| `POST https://stablephone.dev/api/number/topup` | $15.00 | Extend 30 days (stackable) |
| `GET https://stablephone.dev/api/numbers?wallet=0x...` | Free | List your numbers |

Buy a number:

```bash
npx agentcash fetch https://stablephone.dev/api/number -m POST -b '{"area_code": "415", "country_code": "US"}'
```

Both fields optional, US default.

## Voices

| ID | Description |
|---|---|
| `nat` | American female (default) |
| `josh` | Articulate American male |
| `maya` | Young American female, soft |
| `june` | American female |
| `paige` | Calm, soft-tone female |
| `derek` | Soft and engaging male |
| `florian` | German male |

## Typical Flow

1. Make the call:
   ```bash
   npx agentcash fetch https://stablephone.dev/api/call -m POST -b '{"phone_number": "+14155551234", "task": "..."}'
   ```
2. Poll status (free) until `completed: true`:
   ```bash
   npx agentcash fetch https://stablephone.dev/api/call/CALL_ID
   ```
3. Read `summary` and `transcript` from response
