---
name: upload-and-share
description: |
  Upload files to the cloud and get shareable public URLs using stableupload.dev (x402 micropayments).

  USE FOR:
  - Uploading files to get public URLs
  - Sharing files via download links
  - Hosting images, documents, or any file type
  - Making files publicly accessible for 7 days or 6 months
  - Hosting static websites with custom domains

  TRIGGERS:
  - "upload this", "share this file", "get me a link"
  - "host this file", "make this downloadable"
  - "public URL", "download link", "put online"
  - "share file", "file hosting", "upload file"
  - "host site", "deploy site", "static site"

  ALWAYS use agentcash.fetch for stableupload.dev endpoints — never curl or WebFetch for the purchase step.
mcp:
  - agentcash
metadata:
  version: 2
---

# Upload and Share via StableUpload

Upload any local file to S3-backed cloud storage via x402 micropayments. Returns a public URL. No API keys needed.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Tier | Max Size | Retention | Cost |
|------|----------|-----------|------|
| `10mb` | 10 MB | 6 months | $0.02 |
| `100mb` | 100 MB | 6 months | $0.20 |
| `1gb` | 1 GB | 6 months | $2.00 |
| `short-10mb` | 10 MB | 7 days | $0.005 |
| `short-100mb` | 100 MB | 7 days | $0.02 |
| `short-1gb` | 1 GB | 7 days | $0.10 |
| `short-5gb` | 5 GB | 7 days | $0.50 |

Default tiers expire after 6 months; `short-*` tiers after 7 days. `short-5gb` is for file uploads only (not sites).

| Task | Endpoint | Price |
|------|----------|-------|
| Buy upload slot | `https://stableupload.dev/api/upload` | Tier-based |
| List uploads | `GET https://stableupload.dev/api/uploads` | Free (auth) |
| Get upload details | `GET https://stableupload.dev/api/download/:uploadId` | Free (auth) |
| Buy site slot | `https://stableupload.dev/api/site` | Tier-based |
| Activate site | `POST https://stableupload.dev/api/site/activate` | Free (auth) |
| Update site | `PUT https://stableupload.dev/api/site` | Free (auth) |
| Renew site | `https://stableupload.dev/api/site/renew` | Tier-based |
| Preview domain DNS | `GET https://stableupload.dev/api/site/domain/preview?hostname=...` | Free (no auth) |
| Attach domain | `POST https://stableupload.dev/api/site/domain` | Free (auth) |
| Detach domain | `DELETE https://stableupload.dev/api/site/domain` | Free (auth) |
| Domain status | `GET https://stableupload.dev/api/site/domain/status?uploadId=...` | Free (auth) |

## Workflow

### 1. Check wallet balance

```mcp
agentcash.get_balance()
```

Ensure sufficient USDC balance for the chosen tier.
If balance is low and the user needs funding details, call `agentcash.list_accounts()`.

### 2. Determine the tier

Pick the smallest tier that fits the file. Check file size first with `ls -la` or `wc -c`. Use a `short-*` tier when the file only needs to live for 7 days — they cost a fraction of the 6-month tiers.

| Tier | Max Size | Retention | Cost |
|------|----------|-----------|------|
| `10mb` | 10 MB | 6 months | $0.02 |
| `100mb` | 100 MB | 6 months | $0.20 |
| `1gb` | 1 GB | 6 months | $2.00 |
| `short-10mb` | 10 MB | 7 days | $0.005 |
| `short-100mb` | 100 MB | 7 days | $0.02 |
| `short-1gb` | 1 GB | 7 days | $0.10 |
| `short-5gb` | 5 GB | 7 days | $0.50 |

### 3. Buy the upload slot

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/upload",
  method="POST",
  body={"filename": "report.pdf", "contentType": "application/pdf", "tier": "10mb"}
)
```

**Parameters:**
- `filename` — name for the uploaded file (required)
- `contentType` — MIME type (required, advisory for browser)
- `tier` — `"10mb"`, `"100mb"`, `"1gb"`, `"short-10mb"`, `"short-100mb"`, `"short-1gb"`, or `"short-5gb"` (required)
- `policyTtlSeconds` — optional upload URL expiration in seconds, 60–86400; use longer TTLs only when reserving an output slot for a downstream service

**Response:**
```json
{
  "uploadId": "k7gm3nqp2",
  "uploadUrl": "https://f.stableupload.dev/k7gm3nqp2/report.pdf?t=...",
  "uploadMethod": "put",
  "uploadUrlExpiresAt": "2026-08-19T01:00:00.000Z",
  "publicUrl": "https://f.stableupload.dev/k7gm3nqp2/report.pdf",
  "expiresAt": "2027-02-19T00:00:00.000Z",
  "maxSize": 10485760,
  "curlExample": "curl -X PUT ..."
}
```

### 4. Upload the file via curl

Use Bash to PUT the file to the returned `uploadUrl`:

```bash
curl -s -X PUT "<uploadUrl>" -H "Content-Type: <mime>" --data-binary @/absolute/path/to/file
```

**Critical:** Use `--data-binary` (not `-d`) to preserve binary content. Use the absolute path.

The response includes a ready-made `curlExample`. If `uploadMethod` is `"post"`, upload a multipart form to `postUrl` with `postFields` instead of a PUT.

### 5. Share the public URL

Present the `publicUrl` to the user. This URL is publicly accessible immediately and remains live for the tier's retention window (6 months for default tiers, 7 days for `short-*` tiers) — see `expiresAt`.

## Common MIME Types

| File Type | Content Type |
|-----------|-------------|
| PDF | `application/pdf` |
| PNG image | `image/png` |
| JPEG image | `image/jpeg` |
| CSV | `text/csv` |
| JSON | `application/json` |
| Plain text | `text/plain` |
| ZIP archive | `application/zip` |
| Excel | `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` |
| Unknown | `application/octet-stream` |

## Listing Previous Uploads

To list uploads for the current wallet:

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/uploads",
  method="GET"
)
```

## Get Upload Details

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/download/k7gm3nqp2",
  method="GET"
)
```

## Key Details

- **No API keys required** — payment is the authentication
- **Upload URLs expire** — see `uploadUrlExpiresAt` in the response; upload promptly, or set `policyTtlSeconds` (60–86400) at purchase to extend
- **Public URLs last the tier's retention window** from purchase date — 6 months for default tiers, 7 days for `short-*` tiers
- **Any file type accepted** — contentType is advisory for the browser, not a restriction
- **S3-backed** — files stored on AWS S3 with public read access
- **Discovery endpoint**: `agentcash.discover_api_endpoints(url="https://stableupload.dev")` if you need to verify endpoints

## Common Patterns

**Upload a file the user just created:**
Skip discovery, go straight to wallet check + buy slot + upload.

**Upload multiple files:**
Buy separate slots for each file. Slots can be purchased in parallel but uploads must use the correct uploadUrl for each.

**User asks to "share" or "send" a file:**
Upload it and present the public URL. The URL can be shared anywhere.

**Host images for emails:**
Upload the image, then reference the `publicUrl` in email HTML:
```html
<img src="https://f.stableupload.dev/abc/photo.png" alt="Photo" />
```

## Static Site Hosting

Host static sites (HTML/CSS/JS) with custom domains and automatic HTTPS.

### Deploy a Site

**1. Buy a site slot (paid, tier-based):**

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site",
  method="POST",
  body={"filename": "my-site.zip", "tier": "100mb"}
)
```

Returns `{uploadId, uploadUrl}`.

**2. Upload the zip:**

```bash
curl -X PUT "$uploadUrl" -H "Content-Type: application/zip" --data-binary @site.zip
```

**3. Activate the site (SIWX, free):**

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/activate",
  method="POST",
  body={"uploadId": "..."}
)
```

Returns `{siteUrl, fileCount, files}`. Site is live at `https://{uploadId}.s.stableupload.dev/`.

### Update an Existing Site

Update for free: PUT /api/site to get a new upload URL, upload the new zip, then activate.

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site",
  method="PUT",
  body={"uploadId": "...", "filename": "my-site.zip"}
)
```

Then upload the new zip and call activate again.

### Custom Domain

**1. Preview the required DNS records (free, no auth):**

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/domain/preview?hostname=www.example.com",
  method="GET"
)
```

Returns `{hostname, dnsRecords}`. Set these DNS records before attaching for instant SSL.

**2. Attach the domain:**

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/domain",
  method="POST",
  body={"uploadId": "...", "hostname": "www.example.com"}
)
```

Creates the TLS cert and routes the hostname. Check status:

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/domain/status?uploadId=...",
  method="GET"
)
```

Poll until `ssl` is `"active"`.

Detach a domain:

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/domain",
  method="DELETE",
  body={"uploadId": "...", "hostname": "www.example.com"}
)
```

### Renew a Site

```mcp
agentcash.fetch(
  url="https://stableupload.dev/api/site/renew",
  method="POST",
  body={"uploadId": "...", "count": 4}
)
```

Extends the site by `count` x the original tier's retention window (e.g. 4 x 6 months on a default tier). Price = tier price x count ($0.005–$20.00).

### Notes

- Max 500 files per site
- Tier limit applies to uncompressed size
- Sites support all tiers except `short-5gb`; POST /api/site takes `filename` and `tier` only (no `contentType`)
- Custom domains get automatic HTTPS via Cloudflare

## Error Handling

- **Insufficient balance**: Call `agentcash.list_accounts()` to show deposit links and wallet addresses
- **File too large for tier**: Suggest the next tier up
- **Upload URL expired**: Buy a new slot (the previous payment is non-refundable)
- **curl fails**: Verify the file path exists and the uploadUrl is correctly quoted
