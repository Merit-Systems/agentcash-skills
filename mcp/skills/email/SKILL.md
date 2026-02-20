---
name: email
description: |
  Send emails, buy forwarding inboxes, manage custom subdomains, and read messages programmatically via x402.

  USE FOR:
  - Sending emails (one-off or from custom addresses)
  - Buying a forwarding inbox (username@x402email.com)
  - Purchasing custom email subdomains (you@yourname.x402email.com)
  - Reading and managing inbox messages programmatically
  - Setting up catch-all forwarding for subdomains
  - Managing authorized signers for shared subdomains

  TRIGGERS:
  - "send email", "email this to", "notify by email"
  - "buy inbox", "forwarding address", "email inbox"
  - "custom domain", "subdomain", "email subdomain"
  - "read emails", "check inbox", "list messages"
  - "email address", "disposable email", "temporary email"

  ALWAYS use agentcash.fetch for stableemail.dev endpoints.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
mcp:
  - agentcash
---

# Email with StableEmail

Send emails, manage inboxes, and run custom subdomains via x402 payments at `https://stableemail.dev`.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Send email (shared) | `https://stableemail.dev/api/send` | $0.02 |
| Buy subdomain | `https://stableemail.dev/api/subdomain/buy` | $5.00 |
| Send from subdomain | `https://stableemail.dev/api/subdomain/send` | $0.005 |
| Buy inbox | `https://stableemail.dev/api/inbox/buy` | $1.00 |
| Send from inbox | `https://stableemail.dev/api/inbox/send` | $0.005 |
| Top up inbox (30d) | `https://stableemail.dev/api/inbox/topup` | $1.00 |
| Top up inbox (90d) | `https://stableemail.dev/api/inbox/topup/quarter` | $2.50 |
| Top up inbox (365d) | `https://stableemail.dev/api/inbox/topup/year` | $8.00 |
| List inbox messages | `https://stableemail.dev/api/inbox/messages` | $0.001 |
| Read inbox message | `https://stableemail.dev/api/inbox/messages/read` | $0.001 |
| Create subdomain inbox | `https://stableemail.dev/api/subdomain/inbox/create` | $0.25 |
| List subdomain messages | `https://stableemail.dev/api/subdomain/inbox/messages` | $0.001 |
| Read subdomain message | `https://stableemail.dev/api/subdomain/inbox/messages/read` | $0.001 |

### Free Management Endpoints

| Task | Endpoint |
|------|----------|
| Subdomain status | `GET https://stableemail.dev/api/subdomain/status?subdomain=name` |
| Update subdomain | `POST https://stableemail.dev/api/subdomain/update` |
| Manage signers | `POST https://stableemail.dev/api/subdomain/signers` |
| Inbox status | `GET https://stableemail.dev/api/inbox/status?username=name` |
| Update inbox | `POST https://stableemail.dev/api/inbox/update` |
| Cancel inbox | `POST https://stableemail.dev/api/inbox/cancel` |
| Delete message | `POST https://stableemail.dev/api/inbox/messages/delete` |
| List subdomain inboxes | `POST https://stableemail.dev/api/subdomain/inbox/list` |
| Delete subdomain inbox | `POST https://stableemail.dev/api/subdomain/inbox/delete` |
| Update subdomain inbox | `POST https://stableemail.dev/api/subdomain/inbox/update` |
| Delete subdomain message | `POST https://stableemail.dev/api/subdomain/inbox/messages/delete` |

Free endpoints use SIWX wallet authentication (handled automatically by agentcash). Use `agentcash.fetch_with_auth` for free GET endpoints, `agentcash.fetch` for free POST endpoints.

## Send an Email

Send from the shared `relay@x402email.com` address:

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/send",
  method="POST",
  body={
    "to": ["recipient@example.com"],
    "subject": "Hello from x402",
    "html": "<h1>Hi!</h1><p>This email was sent via x402 payments.</p>",
    "text": "Hi! This email was sent via x402 payments.",
    "replyTo": "your-real-email@example.com"
  }
)
```

**Parameters:**
- `to` — array of recipient emails (required)
- `subject` — email subject (required)
- `html` and/or `text` — email body (at least one required)
- `replyTo` — reply-to address (optional but recommended)
- `attachments` — array of `{content, contentType, filename}` (optional, max 5, base64 encoded, ~3.75MB limit each)

**Attachment example:**
```json
{
  "attachments": [{
    "content": "base64-encoded-content...",
    "contentType": "application/pdf",
    "filename": "report.pdf"
  }]
}
```

For calendar invites, use `contentType: "text/calendar; method=REQUEST"`.

## Forwarding Inbox

Buy `username@x402email.com` for $1/month. Emails are forwarded to your real address and/or retained for programmatic access.

### Buy an Inbox

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/buy",
  method="POST",
  body={
    "username": "alice",
    "forwardTo": "alice@gmail.com"
  }
)
```

Omit `forwardTo` to use as a programmatic-only mailbox (retainMessages auto-enabled).

### Send from Inbox

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/send",
  method="POST",
  body={
    "username": "alice",
    "to": ["bob@example.com"],
    "subject": "Hello",
    "html": "<p>Hi Bob</p>",
    "text": "Hi Bob"
  }
)
```

### Top Up Inbox

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/topup",
  method="POST",
  body={"username": "alice"}
)
```

Anyone can top up any inbox. Top-ups stack. Bulk discounts: 90 days/$2.50 (17% off), 365 days/$8 (34% off).

### Cancel and Refund

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/cancel",
  method="POST",
  body={"username": "alice"}
)
```

Returns pro-rata USDC refund on-chain automatically.

## Read Inbox Messages

Enable message retention, then read messages programmatically.

### Enable Retention

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/update",
  method="POST",
  body={"username": "alice", "retainMessages": true}
)
```

### List Messages

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/messages",
  method="POST",
  body={"username": "alice", "limit": 20}
)
```

### Read a Message

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/inbox/messages/read",
  method="POST",
  body={"messageId": "msg_abc123"}
)
```

Returns full message with from, to, subject, date, text, html, and attachments.

## Custom Subdomains

Buy `yourname.x402email.com` for $5 one-time. Send from any address on your subdomain for $0.005/email.

### Buy a Subdomain

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/subdomain/buy",
  method="POST",
  body={"subdomain": "yourname"}
)
```

Rules: 3-30 chars, lowercase alphanumeric + hyphens. DNS verification takes ~5 minutes.

### Send from Subdomain

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/subdomain/send",
  method="POST",
  body={
    "from": "support@yourname.x402email.com",
    "to": ["customer@example.com"],
    "subject": "Your order confirmation",
    "html": "<p>Thank you for your order!</p>"
  }
)
```

### Create Subdomain Inboxes

Create per-address inboxes on your subdomain ($0.25 each, max 100):

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/subdomain/inbox/create",
  method="POST",
  body={
    "subdomain": "yourname",
    "localPart": "support",
    "forwardTo": "team@company.com"
  }
)
```

### Catch-All Forwarding

Forward all unmatched addresses on your subdomain:

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/subdomain/update",
  method="POST",
  body={"subdomain": "yourname", "catchAllForwardTo": "catch-all@company.com"}
)
```

### Manage Authorized Signers

Allow other wallets to send from your subdomain:

```mcp
agentcash.fetch(
  url="https://stableemail.dev/api/subdomain/signers",
  method="POST",
  body={"action": "add", "subdomain": "yourname", "walletAddress": "0x..."}
)
```

## Images in Emails

Host images on StableUpload or AgentUpload, then reference in HTML:

```html
<img src="https://f.stableupload.dev/abc/photo.png" alt="Photo" />
```

Most email clients strip data URIs — always use hosted URLs.

## Workflows

### Quick Send

- [ ] (Optional) Check balance: `agentcash.get_wallet_info`
- [ ] Send email via `/api/send`
- [ ] Confirm delivery via messageId

### Set Up Professional Email

- [ ] Buy subdomain ($5)
- [ ] Wait for DNS verification (~5 min)
- [ ] Create inboxes for team members ($0.25 each)
- [ ] Set up catch-all forwarding
- [ ] Send from custom addresses ($0.005 each)

### Programmatic Mailbox

- [ ] Buy inbox with no forwardTo ($1)
- [ ] Messages are retained automatically
- [ ] List and read messages via API ($0.001 each)
- [ ] Delete messages when processed (free)

## Cost Estimation

| Task | Cost |
|------|------|
| Send one email (shared) | $0.02 |
| Send one email (subdomain/inbox) | $0.005 |
| Buy inbox (30 days) | $1.00 |
| Buy inbox (1 year) | $1.00 + $8.00 = $9.00 |
| Buy subdomain + 5 inboxes | $5.00 + $1.25 = $6.25 |
| Read 100 messages | $0.10 |
