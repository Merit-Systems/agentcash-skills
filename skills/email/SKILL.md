---
name: email
description: Send emails, buy inboxes, and create custom email subdomains. Pay-per-send ($0.02), forwarding inboxes ($1/month), and custom subdomains ($5) with full DKIM/SPF/DMARC. Use when asked to send an email, create an email address, set up email forwarding, read inbox messages, or manage email infrastructure.
---

# StableEmail — Email Delivery & Inboxes

Pay-per-send email. No API keys, no accounts.

## Quick Send ($0.02)

```bash
npx agentcash fetch https://stableemail.dev/api/send -m POST -b '{"to": ["recipient@example.com"], "subject": "Hello", "html": "<p>Hi there</p>"}'
```

Sends from `relay@x402email.com`. Requires `html` or `text` (or both).

Optional: `replyTo`, `attachments` (base64, max 5, ~3.75MB each).

For calendar invites: use `contentType: "text/calendar; method=REQUEST"` in attachments.

## Three Tiers

### 1. Shared Domain ($0.02/email)
- `POST https://stableemail.dev/api/send` — sends from `relay@x402email.com`
- No setup needed, cheapest option

### 2. Forwarding Inbox ($1/month)
- `POST https://stableemail.dev/api/inbox/buy` — buy `username@x402email.com` ($1, 30 days)
- `POST https://stableemail.dev/api/inbox/send` — send from your inbox ($0.005)
- `POST https://stableemail.dev/api/inbox/messages` — read received emails ($0.001)
- Can forward to a real address and/or retain messages for API access

### 3. Custom Subdomain ($5 one-time)
- `POST https://stableemail.dev/api/subdomain/buy` — buy `yourname.x402email.com` ($5)
- `POST https://stableemail.dev/api/subdomain/send` — send from any address on it ($0.005)
- `POST https://stableemail.dev/api/subdomain/inbox/create` — create per-address inboxes ($0.25)
- Full DKIM/SPF/DMARC, up to 100 inboxes per subdomain

## Inbox Management

### Forwarding Inbox Endpoints

| Endpoint | Price | Purpose |
|---|---|---|
| `POST https://stableemail.dev/api/inbox/buy` | $1.00 | Buy inbox (30 days) |
| `POST https://stableemail.dev/api/inbox/send` | $0.005 | Send from inbox |
| `POST https://stableemail.dev/api/inbox/topup` | $1.00 | Extend 30 days |
| `POST https://stableemail.dev/api/inbox/topup/quarter` | $2.50 | Extend 90 days (save 17%) |
| `POST https://stableemail.dev/api/inbox/topup/year` | $8.00 | Extend 365 days (save 34%) |
| `POST https://stableemail.dev/api/inbox/messages` | $0.001 | List messages |
| `POST https://stableemail.dev/api/inbox/messages/read` | $0.001 | Read a message |
| `GET https://stableemail.dev/api/inbox/status?username=...` | Free | Check inbox status |
| `POST https://stableemail.dev/api/inbox/update` | Free | Update settings |
| `POST https://stableemail.dev/api/inbox/cancel` | Free | Cancel with pro-rata refund |
| `POST https://stableemail.dev/api/inbox/messages/delete` | Free | Delete a message |

### Subdomain Endpoints

| Endpoint | Price | Purpose |
|---|---|---|
| `POST https://stableemail.dev/api/subdomain/buy` | $5.00 | Buy subdomain |
| `POST https://stableemail.dev/api/subdomain/send` | $0.005 | Send from subdomain |
| `GET https://stableemail.dev/api/subdomain/status?subdomain=...` | Free | Check DNS/SES status |
| `POST https://stableemail.dev/api/subdomain/signers` | Free | Add/remove authorized wallets |
| `POST https://stableemail.dev/api/subdomain/update` | Free | Update catch-all forwarding |
| `POST https://stableemail.dev/api/subdomain/inbox/create` | $0.25 | Create inbox on subdomain |
| `POST https://stableemail.dev/api/subdomain/inbox/list` | Free | List subdomain inboxes |
| `POST https://stableemail.dev/api/subdomain/inbox/messages` | $0.001 | List inbox messages |
| `POST https://stableemail.dev/api/subdomain/inbox/messages/read` | $0.001 | Read a message |

## Tips

- Free endpoints (status, update, cancel, delete) require wallet signature auth — agentcash handles this automatically
- Anyone can top up any inbox — no wallet ownership required
- DNS verification takes ~5 minutes after subdomain purchase
- For images in HTML emails, host on StableUpload first, then use `<img src="url">`
- Inbox message retention requires `retainMessages: true` (set via `POST https://stableemail.dev/api/inbox/update`)
- Max 500 messages per inbox, warning at 80% capacity
